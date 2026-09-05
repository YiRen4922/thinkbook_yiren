## deque源码剖析 · 《STL源码剖析》笔记

> deque（double-ended queue，双端队列）是STL中一种**双向开口**的序列式容器，允许在常数时间内对头尾两端进行元素的插入和删除操作。本节基于SGI STL源码及侯捷《STL源码剖析》，深入剖析deque的底层实现原理。


### 1. deque的整体架构

#### 1.1 类继承关系

deque容器**保护继承**于类模板`_Deque_base`，内存分配和释放均通过基类完成。容器首地址和迭代器等保存在结构体成员变量`_M_impl`中，它继承于别名类型`_Tp_alloc_type`，最终的内存分配通过它完成。

```cpp
template <class _Tp, class _Alloc = __STL_DEFAULT_ALLOCATOR(_Tp)>
class deque : protected _Deque_base<_Tp, _Alloc> {
    // ...
};
```

#### 1.2 deque vs vector

deque和vector的设计哲学截然不同：

| 对比维度 | deque | vector |
|----------|-------|--------|
| 开口方向 | **双向开口**（头尾均可操作） | 单向开口（仅尾部高效） |
| 容量概念 | **没有容量**，动态分段组合 | 有容量，扩容需重新分配整块内存 |
| 头部插入/删除 | $O(1)$ | $O(n)$（需移动所有元素） |
| 内存连续性 | **逻辑连续**，物理分段 | 物理连续 |
| reserve功能 | ❌ 无 | ✅ 有 |
| 迭代器类型 | Random Access Iterator | Random Access Iterator |

> deque没有`reserve`功能，因为它由分段连续空间组合而成，随时可以增加新的空间并链接起来。


### 2. 中控器（Map）

#### 2.1 为什么需要中控器？

deque由一段一段的**定量连续空间**（称为缓冲区）构成。为了管理这些分段空间，deque引入了**map**（中控器）——它是一小块连续空间，其中每个元素都是指向缓冲区的指针。

```
┌─────────────────────────────────────────────────────────────┐
│                       中控器 (Map)                         │
│  ┌──────┬──────┬──────┬──────┬──────┬──────┬──────┐       │
│  │ ptr  │ ptr  │ ptr  │ ptr  │ ptr  │ ptr  │ ptr  │       │
│  └──┬───┴──┬───┴──┬───┴──┬───┴──┬───┴──┬───┴──┬───┘       │
│     │      │      │      │      │      │      │           │
│     ▼      ▼      ▼      ▼      ▼      ▼      ▼           │
│  ┌─────┐┌─────┐┌─────┐┌─────┐┌─────┐┌─────┐┌─────┐      │
│  │buf 0││buf 1││buf 2││buf 3││buf 4││buf 5││buf 6│      │
│  └─────┘└─────┘└─────┘└─────┘└─────┘└─────┘└─────┘      │
│  <─────────── 缓冲区（实际存储数据）─────────────────>     │
└─────────────────────────────────────────────────────────────┘
```

#### 2.2 核心数据结构

SGI STL中deque的核心成员：

```cpp
template <class T, class Alloc = alloc, size_t BufSiz = 0>
class deque {
protected:
    typedef pointer* map_pointer;
    map_pointer map;          // 指向中控器的指针（指针的指针）
    size_type map_size;       // 中控器可容纳的指针数量
    iterator start;           // 指向第一个元素的迭代器
    iterator finish;          // 指向最后一个元素的后一个位置
};
```

> 由于缓冲区本身就是指针，所以`map`的类型是`_Tp**`（指针的指针）。

#### 2.3 缓冲区大小计算

SGI STL中缓冲区大小按如下规则计算：

```cpp
inline size_t __deque_buf_size(size_t __size) {
    return (__size < 512) 
           ? size_t(512 / __size)   // 元素小于512字节：容纳 512/sizeof(T) 个元素
           : size_t(1);              // 元素大于等于512字节：只容纳 1 个元素
}
```

> 如果元素类型小于512字节，缓冲区可容纳 `512 / sizeof(T)` 个元素；若元素类型大于等于512字节，缓冲区只容纳 **1** 个元素。

用户可通过deque的第三个模板参数指定缓冲区大小，默认值0表示使用512字节。

#### 2.4 中控器的构造与扩容

**初始构造时**：map的节点数量根据元素数量决定：
```
节点数量 = max(元素数量 / buffer_size + 2, 8)
```

即使指定元素数量为0，也会默认创建 **8个节点**。这个设计保证头尾两端都有足够的预留空间，使得`push_front`和`push_back`能在常数时间内完成。

**扩容时**：当map本身的空间不足以容纳新的缓冲区指针时，需要**重新分配一个更大的map**，并将原来的指针数组复制过去。这个操作**只移动指针**（不移动缓冲区中的数据），代价相对较低。


### 3. 迭代器

> deque维护“整体连续”假象的使命，主要落在迭代器的`operator++`和`operator--`身上。

#### 3.1 为什么需要自定义迭代器？

deque维护的空间在整体上并不是连续的，因此deque的迭代器不能像vector一样使用普通的指针，它必须能够满足从一个连续空间跳到另一个连续空间的功能。

#### 3.2 迭代器的四个核心成员

```cpp
template <class _Tp, class _Ref, class _Ptr>
struct _Deque_iterator {
    typedef _Tp** _Map_pointer;
    
    _Tp* _M_cur;        // 指向当前缓冲区中的当前位置
    _Tp* _M_first;      // 指向当前缓冲区的起始位置
    _Tp* _M_last;       // 指向当前缓冲区的末尾（最后一个元素的下一个位置）
    _Map_pointer _M_node; // 指向当前缓冲区在中控器中的位置
};
```

#### 3.3 迭代器的核心操作

**`set_node`：跳转到指定缓冲区**

```cpp
void _M_set_node(_Map_pointer __new_node) {
    _M_node = __new_node;
    _M_first = *__new_node;
    _M_last = _M_first + difference_type(buffer_size());
}
```

**`operator++`：前进一个位置**

```cpp
_Self& operator++() {
    ++_M_cur;
    if (_M_cur == _M_last) {        // 到达当前缓冲区末尾
        _M_set_node(_M_node + 1);   // 跳到下一个缓冲区
        _M_cur = _M_first;          // 指向新缓冲区的起始位置
    }
    return *this;
}
```

**`operator+=`：随机移动 n 个距离**

```cpp
_Self& operator+=(difference_type __n) {
    difference_type __offset = __n + (_M_cur - _M_first);
    if (__offset >= 0 && __offset < difference_type(buffer_size())) {
        // 在同一缓冲区内部移动
        _M_cur += __n;
    } else {
        // 需要跨缓冲区
        difference_type __node_offset = 
            __offset > 0 ? __offset / buffer_size() 
                         : -difference_type((-__offset - 1) / buffer_size()) - 1;
        _M_set_node(_M_node + __node_offset);
        _M_cur = _M_first + (__offset - __node_offset * buffer_size());
    }
    return *this;
}
```

> 迭代器需要在中控器和缓冲区之间跳转，这是deque迭代器比vector迭代器复杂许多的根本原因。

#### 3.4 迭代器的恒常特性（invariants）

SGI STL源码中明确说明了deque迭代器的恒常特性：

- `i.node`是map array中某个元素的地址
- `i.node`所指内容是一个指针，指向某个缓冲区的头
- `i.first == *(i.node)`
- `i.last == i.first + buffer_size()`
- `i.cur`指向范围`[i.first, i.last)`之间

**重要结论**：empty deque一定会有一个node，而具有N个元素的deque（N表示缓冲区大小），一定会有两个nodes。

#### 3.5 迭代器类型

deque的迭代器属于**随机访问迭代器**（`random_access_iterator_tag`）。但需注意，其随机访问的复杂度**高于**vector——每次跨缓冲区访问都需要通过中控器间接寻址。


### 4. 构造与内存管理

#### 4.1 构造函数

deque的构造函数最终调用`_M_initialize_map`：

```cpp
void _M_initialize_map(size_type __n) {
    // 计算需要的节点数
    size_type __num_nodes = __n / buffer_size() + 1;
    // map_size = max(初始map大小, __num_nodes + 2)
    _M_map_size = max(initial_map_size(), __num_nodes + 2);
    // 分配map空间
    _M_map = _M_allocate_map(_M_map_size);
    // 将节点放在map的中央位置，为两端预留空间
    _Map_pointer __nstart = _M_map + (_M_map_size - __num_nodes) / 2;
    _Map_pointer __nfinish = __nstart + __num_nodes;
    // 为每个节点分配缓冲区
    _M_create_nodes(__nstart, __nfinish);
    // 设置start和finish迭代器
    _M_start._M_set_node(__nstart);
    _M_finish._M_set_node(__nfinish - 1);
    _M_start._M_cur = _M_start._M_first;
    _M_finish._M_cur = _M_finish._M_first + __n % buffer_size();
}
```

#### 4.2 为什么将节点放在map中央？

将节点放在map的中央位置，为头尾两端都预留了空间，使得头尾插入时无需频繁扩容：

```
┌─────────────────────────────────────────────────────┐
│  map                                               │
│  ┌────┬────┬────┬────┬────┬────┬────┬────┐        │
│  │    │    │    │buf0│buf1│buf2│    │    │        │
│  └────┴────┴────┴──┬─┴──┬─┴──┬─┴────┴────┘        │
│                     │    │    │                     │
│               预留空间 │  数据  │  预留空间           │
│                (头部) │ 节点  │  (尾部)             │
└─────────────────────────────────────────────────────┘
```


### 5. 基本操作源码剖析

#### 5.1 push_back（尾部插入）

`push_back`首先检查尾部缓冲区是否有剩余空间：

```cpp
void push_back(const value_type& __x) {
    if (_M_finish._M_cur != _M_finish._M_last - 1) {
        // 尾部缓冲区还有空间：直接在当前位置构造
        _Construct(_M_finish._M_cur, __x);
        ++_M_finish._M_cur;
    } else {
        // 尾部缓冲区已满：调用 _M_push_back_aux
        _M_push_back_aux(__x);
    }
}
```

**`_M_push_back_aux`的逻辑**：
1. 检查map尾部是否还有预留空间存放新缓冲区指针
2. 若没有，则重新配置map（扩容）
3. 分配一个新的缓冲区
4. 在新缓冲区的第一个位置构造元素
5. 更新`_M_finish`迭代器

#### 5.2 push_front（头部插入）

```cpp
void push_front(const value_type& __x) {
    if (_M_start._M_cur != _M_start._M_first) {
        // 头部缓冲区还有空间：直接从后往前插入
        _Construct(_M_start._M_cur - 1, __x);
        --_M_start._M_cur;
    } else {
        // 头部缓冲区已满：调用 _M_push_front_aux
        _M_push_front_aux(__x);
    }
}
```

**`_M_push_front_aux`的逻辑**：
1. 检查map头部是否还有预留空间
2. 若没有，重新配置map
3. 分配一个新的缓冲区
4. 在新缓冲区的最后一个位置构造元素
5. 更新`_M_start`迭代器

#### 5.3 pop_back（尾部删除）

```cpp
void pop_back() {
    if (_M_finish._M_cur != _M_finish._M_first) {
        // 尾部缓冲区还有元素：直接析构
        --_M_finish._M_cur;
        _Destroy(_M_finish._M_cur);
    } else {
        // 当前缓冲区已空：释放缓冲区并跳到前一个
        _M_pop_back_aux();
    }
}
```

**`_M_pop_back_aux`的逻辑**：
1. 释放当前尾部缓冲区
2. 将`_M_finish`指向map中的前一个节点
3. 将`_M_finish._M_cur`指向新缓冲区的末尾
4. 析构最后一个元素

#### 5.4 pop_front（头部删除）

```cpp
void pop_front() {
    if (_M_start._M_cur != _M_start._M_last - 1) {
        // 头部缓冲区还有元素：直接析构
        _Destroy(_M_start._M_cur);
        ++_M_start._M_cur;
    } else {
        // 当前缓冲区只剩一个元素：释放缓冲区并跳到下一个
        _M_pop_front_aux();
    }
}
```

#### 5.5 insert（中间插入）

`insert`在指定位置插入元素，deque采用**移动较少元素**的策略：

```cpp
iterator insert(iterator __position, const value_type& __x) {
    if (__position._M_cur == _M_start._M_cur) {
        // 插入位置在头部：直接push_front
        push_front(__x);
        return _M_start;
    } else if (__position._M_cur == _M_finish._M_cur) {
        // 插入位置在尾部：直接push_back
        push_back(__x);
        return _M_finish - 1;
    } else {
        // 中间插入：调用 _M_insert_aux
        return _M_insert_aux(__position, __x);
    }
}
```

**`_M_insert_aux`的策略**：
1. 计算插入点之前的元素个数和之后的元素个数
2. **选择元素较少的一端进行移动**，以最小化拷贝次数
3. 若插入点离头部近，移动头部元素；若离尾部近，移动尾部元素
4. 在腾出的位置构造新元素

> `insert`效率不高，由于deque的“分段连续”特性，在中间位置插入/删除的性能**比vector更差**。

#### 5.6 erase（删除）

```cpp
iterator erase(iterator __position) {
    iterator __next = __position;
    ++__next;
    // 计算删除位置前后的元素个数
    difference_type __n = __position - _M_start;
    if (__n < size() - __n) {
        // 前面的元素少：移动前面的元素
        copy_backward(_M_start, __position, __next);
        pop_front();
    } else {
        // 后面的元素少：移动后面的元素
        copy(__next, _M_finish, __position);
        pop_back();
    }
    return _M_start + __n;
}
```

> 与insert类似，erase也采用**移动较少元素**的策略，时间复杂度为$O(\min(n, size()-n))$。

#### 5.7 下标访问（operator[]）

```cpp
reference operator[](size_type __n) {
    // 计算在哪个缓冲区，以及缓冲区内的偏移
    // __n 在 __n / buffer_size() 号缓冲区
    // 偏移量为 __n % buffer_size()
    return _M_start[_M_node + __n / buffer_size()][__n % buffer_size()];
}
```

> 下标访问需要**两次指针解引用**，因此效率低于vector的一次解引用。


### 6. 性能总结

| 操作 | 时间复杂度 | 说明 |
|------|-----------|------|
| 头部插入/删除（push_front/pop_front） | $O(1)$ | 核心优势，直接在头部缓冲区操作 |
| 尾部插入/删除（push_back/pop_back） | $O(1)$ | 核心优势，直接在尾部缓冲区操作 |
| 中间插入/删除（insert/erase） | $O(n)$ | 需移动元素，选择移动较少的一端 |
| 随机访问（operator[]） | $O(1)$ | 间接访问，需两次指针解引用 |
| 内存扩容 | 稳定（无整体搬移） | 分段分配新块，不移动已有元素 |

> [!important] 使用建议
> 除非必要，**应尽量使用vector**。deque的迭代器比vector复杂，随机访问的代价也更高。对deque进行排序时，最高效的做法是**将deque复制到vector，排序完成后再复制回deque**。


### 7. 源码位置

SGI STL中deque的核心实现在以下文件中：
- **声明**：`bits/stl_deque.h`
- **实现**：`bits/deque.tcc`


### Obsidian 双向链接建议

- [[stack与queue源码剖析]]：stack和queue以deque为默认底层容器
- [[栈]]：栈的ADT定义与基本操作
- [[队列]]：队列的ADT定义与基本操作
- [[vector源码剖析]]：对比deque与vector的内存模型差异

---

如需我继续展开 **“deque的迭代器失效规则”** 或 **“deque与vector的选择指南”** ，随时告诉我。