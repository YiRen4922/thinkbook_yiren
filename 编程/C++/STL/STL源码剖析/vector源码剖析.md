## vector源码剖析 · 《STL源码剖析》笔记

> vector是STL中最常用的序列式容器，其数据安排与操作方式与C原生数组极为相似，但核心区别在于**vector是动态空间**，内部可以自行扩充以容纳新元素。本节基于SGI STL源码及侯捷《STL源码剖析》，深入剖析vector的底层实现原理。


### 1. vector的整体架构

#### 1.1 类继承关系

vector容器**保护继承**于类模板`_Vector_base`，内存的分配与释放均由基类负责。

`_Vector_base`的设计主要有两个目的：
1. **分离内存管理与对象构造**：构造和析构只分配/释放空间而不初始化/清理对象，这使得异常安全更容易
2. **封装配置器差异**：通过条件编译封装了SGI风格配置器与STL标准风格配置器之间的差异

```cpp
template <class _Tp, class _Alloc = __STL_DEFAULT_ALLOCATOR(_Tp)>
class vector : protected _Vector_base<_Tp, _Alloc> {
    // ...
};
```

#### 1.2 vector vs array

| 对比维度 | vector | array / C数组 |
|----------|--------|---------------|
| 空间性质 | **动态空间**，可自行扩充 | 静态空间，容量固定 |
| 容量管理 | 自动管理，用户无需关心 | 需手动分配更大的空间并复制数据 |
| 头部插入/删除 | $O(n)$（需移动所有元素） | $O(n)$ |
| 尾部插入/删除 | 均摊$O(1)$ | 需手动维护 |
| 内存连续性 | 物理连续 | 物理连续 |

> vector的实现技术，关键在于其对**大小的控制**以及**重新分配时的数据移动效率**。


### 2. 核心数据结构：三个迭代器（指针）

vector维护的是一块连续的线性空间，其数据结构极其简单——**三个迭代器（即三个原生指针）** 即可完整描述整个空间的状态。

#### 2.1 三个核心指针

```cpp
template <class T, class Alloc = alloc>
class vector {
protected:
    iterator start;           // 指向目前使用空间的头
    iterator finish;          // 指向目前使用空间的尾（最后一个元素的下一个位置）
    iterator end_of_storage;  // 指向目前可用空间的尾（已分配空间的末尾）
};
```

#### 2.2 内存布局示意图

```
  start                                    finish                              end_of_storage
    ↓                                         ↓                                       ↓
  ┌──────┬──────┬──────┬──────┬──────┬──────┬──────┬──────┬──────┬──────┐
  │  a1  │  a2  │  a3  │  a4  │      │      │      │      │      │      │
  └──────┴──────┴──────┴──────┴──────┴──────┴──────┴──────┴──────┴──────┘
  <────────────── 使用空间 (size = 4) ──────────────><── 备用空间 (capacity - size) ──>
  <────────────────────────── 已分配空间 (capacity) ──────────────────────────────>
```

#### 2.3 核心接口实现

凭借这三个指针，vector可以轻松提供大部分基础功能：

```cpp
iterator begin() { return start; }
iterator end() { return finish; }
size_type size() const { return size_type(end() - begin()); }
size_type capacity() const { return size_type(end_of_storage - begin()); }
bool empty() const { return begin() == end(); }
reference operator[](size_type n) { return *(begin() + n); }
reference front() { return *begin(); }
reference back() { return *(end() - 1); }
```


### 3. 迭代器

vector维护的是连续线性空间，因此其迭代器就是**原生指针（ordinary pointer）** 。

```cpp
typedef value_type* iterator;        // 迭代器就是原生指针 T*
typedef const value_type* const_iterator;
```

> vector的迭代器是原生指针，意味着它满足RandomAccessIterator的全部必要条件。因此，vector支持所有指针算术运算：`p+n`、`p-n`、`p[n]`、`p1-p2`、`p1 < p2` 等。

也正因为迭代器是原生指针，**vector的迭代器在插入/删除时极易失效**——扩容后所有指针指向的地址都变了。


### 4. 构造与内存管理

#### 4.1 基类`_Vector_base`的内存操作

`_Vector_base`封装了vector的底层内存管理：

```cpp
template <class _Tp, class _Alloc>
class _Vector_base {
protected:
    _Tp* _M_start;           // 目前使用空间的头
    _Tp* _M_finish;          // 目前使用空间的尾
    _Tp* _M_end_of_storage;  // 目前可用空间的尾
    
    typedef simple_alloc<_Tp, _Alloc> _M_data_allocator;  // SGI空间配置器
    
    _Tp* _M_allocate(size_t __n) {
        return _M_data_allocator::allocate(__n);          // 分配空间
    }
    
    void _M_deallocate(_Tp* __p, size_t __n) {
        _M_data_allocator::deallocate(__p, __n);          // 释放空间
    }
};
```

#### 4.2 构造过程

vector的构造过程本质上是**先分配内存，再在内存上构造对象**：

```cpp
// 指定大小和初值的构造函数
vector(size_type n, const T& value) { fill_initialize(n, value); }

void fill_initialize(size_type n, const T& value) {
    start = allocate_and_fill(n, value);   // 分配并填充
    finish = start + n;
    end_of_storage = finish;
}

iterator allocate_and_fill(size_type n, const T& x) {
    iterator result = data_allocator::allocate(n);      // 1. 分配内存
    uninitialized_fill_n(result, n, x);                 // 2. 在未初始化内存上构造对象
    return result;
}
```

#### 4.3 析构过程

```cpp
~vector() {
    destroy(start, finish);    // 1. 析构所有对象（调用析构函数）
    deallocate();              // 2. 释放内存（归还给空间配置器）
}
```

> [!important] 内存与对象分离
> vector将**内存分配**（`allocate`）和**对象构造**（`construct`）分离：
> - 分配的内存是**未初始化的原始内存**
> - 对象构造使用**定位new表达式**（placement new）在已分配内存上显式调用构造函数
> - 这种分离使得vector能够推迟对象构造到真正需要的时候，并提高异常安全性

#### 4.4 空间配置器（Allocator）

SGI STL的vector默认使用**第二级空间配置器**（`__default_alloc_template`），具备**次配置能力（sub-allocation）** ：

- 内存来自**内存池（memory pool）** 
- 分配出的空间是**未初始化的**，由`construct()`负责构造
- 释放时由`destroy()`负责析构，`deallocate()`负责归还内存


### 5. 基本操作源码剖析

#### 5.1 push_back（尾部插入）

`push_back`首先检查是否还有备用空间：

```cpp
void push_back(const T& x) {
    if (finish != end_of_storage) {        // 有备用空间
        construct(finish, x);              // 在备用空间上构造元素
        ++finish;
    } else {                               // 无备用空间，需要扩容
        insert_aux(end(), x);
    }
}
```

#### 5.2 扩容机制（核心）

当备用空间不足时，vector会触发扩容——这是vector最核心的机制：

```cpp
template <class T, class Alloc>
void vector<T, Alloc>::insert_aux(iterator position, const T& x) {
    if (finish != end_of_storage) {
        // 有备用空间（略）
    } else {
        // ⭐ 无备用空间：扩容
        const size_type old_size = size();
        const size_type len = old_size != 0 ? 2 * old_size : 1;  // 2倍扩容
        iterator new_start = data_allocator::allocate(len);      // 1. 分配新空间
        iterator new_finish = new_start;
        
        // 2. 将原数据拷贝到新空间
        new_finish = uninitialized_copy(start, position, new_start);
        construct(new_finish, x);                               // 插入新元素
        ++new_finish;
        new_finish = uninitialized_copy(position, finish, new_finish);
        
        // 3. 析构并释放原空间
        destroy(start, finish);
        deallocate();
        
        // 4. 调整迭代器指向新空间
        start = new_start;
        finish = new_finish;
        end_of_storage = new_start + len;
    }
}
```

**扩容三步走**：

| 步骤 | 操作 | 说明 |
|------|------|------|
| ① | **分配新空间** | 通常为原容量的**2倍**（GCC实现） |
| ② | **移动数据** | 将旧空间的元素**拷贝/移动**到新空间 |
| ③ | **释放旧空间** | 析构旧元素并释放内存 |

> [!warning] 扩容代价
> 扩容过程**工程浩大**：
> - 所有元素需要**拷贝/移动**到新空间，时间复杂度$O(n)$
> - 扩容会导致**所有迭代器失效**（包括`begin()`、`end()`及所有指针、引用）
> - 因此，如果已知元素数量，应使用`reserve()`预分配空间以避免多次扩容

**扩容因子对比**：

| 编译器 | 扩容因子 | 说明 |
|--------|----------|------|
| GCC (libstdc++) | **2倍** | `old_size != 0 ? 2 * old_size : 1` |
| MSVC | **1.5倍** | 减少内存碎片 |

#### 5.3 pop_back（尾部删除）

```cpp
void pop_back() {
    --finish;        // 将尾端标记前移一个位置
    destroy(finish); // 析构被放弃的元素
}
```

> `pop_back`只是将`finish`前移并析构对象，**不释放内存**，容量不变。

#### 5.4 insert（任意位置插入）

```cpp
iterator insert(iterator position, const T& x) {
    size_type n = position - start;
    if (finish != end_of_storage && position == finish) {
        // 插入位置在尾部且有备用空间 → 直接push_back
        construct(finish, x);
        ++finish;
    } else {
        // 调用 insert_aux（可能触发扩容）
        insert_aux(position, x);
    }
    return start + n;
}
```

#### 5.5 erase（删除指定位置元素）

```cpp
iterator erase(iterator position) {
    if (position + 1 != finish) {
        copy(position + 1, finish, position);  // 后续元素前移
    }
    --finish;
    destroy(finish);
    return position;
}
```

**时间复杂度**：删除位置之后的所有元素都需要前移，$O(n)$。

#### 5.6 clear（清空所有元素）

```cpp
void clear() {
    erase(start, finish);   // 析构所有元素，size变为0
}
```

> `clear`**不释放内存**，只是析构对象并将`finish`重置为`start`，容量不变。


### 6. 性能总结

| 操作 | 时间复杂度 | 说明 |
|------|-----------|------|
| 尾部插入（push_back） | 均摊$O(1)$ | 扩容时$O(n)$，但均摊为$O(1)$ |
| 尾部删除（pop_back） | $O(1)$ | 只调整指针，不释放内存 |
| 任意位置插入（insert） | $O(n)$ | 需移动插入位置之后的元素 |
| 任意位置删除（erase） | $O(n)$ | 需移动删除位置之后的元素 |
| 随机访问（operator[]） | $O(1)$ | 直接指针偏移 |
| 按值查找（find） | $O(n)$ | 线性遍历 |
| 扩容 | $O(n)$ | 拷贝所有元素到新空间 |


### 7. vector的适用场景

| 场景 | 是否推荐 | 原因 |
|------|---------|------|
| 频繁尾部插入/删除 | ✅ **强烈推荐** | 均摊$O(1)$，效率最高 |
| 频繁随机访问 | ✅ **强烈推荐** | $O(1)$直接访问 |
| 频繁任意位置插入/删除 | ❌ 不推荐 | 需移动大量元素，$O(n)$ |
| 频繁头部插入/删除 | ❌ 不推荐 | 需移动所有元素，$O(n)$ |
| 元素数量未知但可预估 | ✅ 推荐 | 可用`reserve()`预分配，避免多次扩容 |


### Obsidian 双向链接建议

- `[[deque源码剖析]]`：对比vector与deque的内存模型差异
- `[[stack与queue源码剖析]]`：stack可用vector做底层，queue不可以
- `[[空间配置器allocator]]`：vector内存分配依赖的底层机制
- `[[迭代器失效]]`：vector扩容导致迭代器全部失效

---

如需我继续展开 **“vector的迭代器失效规则详解”** 或 **“vector与deque的选择指南”** ，随时告诉我。