## stack与queue源码剖析 · 《STL源码剖析》笔记

> 本节基于SGI STL源码及侯捷《STL源码剖析》，深入分析容器适配器 `stack` 与 `queue` 的底层实现原理。


### 1. 容器适配器（Container Adapter）的本质

#### 1.1 为什么stack和queue不被称为容器？

在SGI STL中，`stack`和`queue`**不被归类为容器（container）**，而被归类为**容器适配器（container adapter）**。它们本身不实现底层存储逻辑，而是对已有顺序容器进行封装，对外提供严格的LIFO/FIFO语义。

**核心思想**：`stack`和`queue`的所有操作都**完全委托**给底层容器对象，自身只做**接口裁剪与语义约束**——这是典型的**适配器设计模式（Adapter Pattern）** 。

> 由于stack和queue没有自己的数据结构，而是借用deque实现，因此从技术上不把这两个容器叫作容器，而是叫作**容器的适配器（container adapters）**。

#### 1.2 适配器的三种类型

STL中的适配器主要分为三种：
1. **容器适配器**：`stack`、`queue`、`priority_queue`
2. **迭代器适配器**：`reverse_iterator`、`insert_iterator`等
3. **仿函数适配器**：`bind2nd`、`not1`等


### 2. stack源码剖析

#### 2.1 模板声明与核心成员

SGI STL中`stack`的模板声明：

```cpp
template <class T, class Sequence = deque<T> >
class stack {
    friend bool operator== (const stack&, const stack&);
    friend bool operator<  (const stack&, const stack&);

public:
    typedef typename Sequence::value_type      value_type;
    typedef typename Sequence::size_type       size_type;
    typedef typename Sequence::reference       reference;
    typedef typename Sequence::const_reference const_reference;
    typedef Sequence container_type;

protected:
    Sequence c;    // 底层容器 —— 一切操作都委托给它

public:
    bool empty() const { return c.empty(); }
    size_type size() const { return c.size(); }
    reference top() { return c.back(); }
    const_reference top() const { return c.back(); }
    void push(const value_type& x) { c.push_back(x); }
    void pop() { c.pop_back(); }
};
```

> [!important] 源码关键点
> 1. **`Sequence c`**：唯一成员变量，所有操作都委托给它
> 2. **`top()`返回引用**：允许直接修改栈顶元素
> 3. **`pop()`不返回值**：若需获取栈顶元素，必须先调`top()`再调`pop()`
> 4. **无迭代器**：栈不允许遍历
> 5. **比较操作**：`==`和`<`直接比较底层容器

#### 2.2 底层容器的准入条件

`stack`要求底层容器支持以下操作：

| 必需操作 | 说明 |
|----------|------|
| `back()` | 访问栈顶元素 |
| `push_back()` | 入栈（尾部插入） |
| `pop_back()` | 出栈（尾部删除） |
| `empty()` / `size()` | 判空与获取长度 |

STL中满足条件的容器有：`deque`（默认）、`vector`、`list`。

> [!tip] 为什么不用`vector`作为默认底层？
> `vector`虽然满足接口要求，但扩容时有整体搬移的性能抖动；`deque`分段存储，扩容时只分配新块，不搬移已有元素，性能更稳定。

#### 2.3 指定其他底层容器

用户可以手动指定底层容器：

```cpp
stack<int, list<int>> s1;   // 用 list 做底层
stack<int, vector<int>> s2; // 用 vector 做底层（可行，但不推荐）
```


### 3. queue源码剖析

#### 3.1 模板声明与核心成员

SGI STL中`queue`的模板声明：

```cpp
template <class T, class Sequence = deque<T> >
class queue {
    friend bool operator== (const queue&, const queue&);
    friend bool operator<  (const queue&, const queue&);

public:
    typedef typename Sequence::value_type      value_type;
    typedef typename Sequence::size_type       size_type;
    typedef typename Sequence::reference       reference;
    typedef typename Sequence::const_reference const_reference;
    typedef Sequence container_type;

protected:
    Sequence c;    // 底层容器 —— 一切操作都委托给它

public:
    bool empty() const { return c.empty(); }
    size_type size() const { return c.size(); }
    reference front() { return c.front(); }
    const_reference front() const { return c.front(); }
    reference back() { return c.back(); }
    const_reference back() const { return c.back(); }
    void push(const value_type& x) { c.push_back(x); }
    void pop() { c.pop_front(); }
};
```

> [!important] 源码关键点
> 1. **`front()`和`back()`**：分别返回队头和队尾元素的引用
> 2. **`pop()`使用`pop_front()`**：从头部删除，符合FIFO原则
> 3. **无迭代器**：队列不允许遍历

#### 3.2 底层容器的准入条件

`queue`要求底层容器支持以下操作：

| 必需操作 | 说明 |
|----------|------|
| `front()` / `back()` | 访问队头/队尾元素 |
| `push_back()` | 入队（尾部插入） |
| `pop_front()` | 出队（头部删除） |
| `empty()` / `size()` | 判空与获取长度 |

STL中满足条件的容器有：`deque`（默认）、`list`。

> [!warning] `vector`不能作为`queue`的底层容器
> `vector`不支持`pop_front()`操作（头部删除为$O(n)$），因此无法适配为`queue`的底层容器。


### 4. 为什么默认底层容器是`deque`？

`stack`和`queue`默认以`deque`作为底部结构，这是综合性能、内存与稳定性的最优选择。

| 对比项 | `deque`（默认） | `vector` | `list` |
|--------|----------------|----------|--------|
| **尾部插入稳定性** | 稳定$O(1)$，无扩容抖动 | 均摊$O(1)$，扩容需搬移 | $O(1)$ |
| **头部删除** | $O(1)$ | $O(n)$（不支持） | $O(1)$ |
| **内存连续性** | 分段连续 | 整块连续 | 不连续 |
| **缓存局部性** | 好 | 最好 | 差 |
| **内存开销** | 低 | 最低 | 高（每个结点两个指针） |

**选择`deque`的核心原因**：

1. **双端操作效率**：`deque`在头尾两端插入/删除均为$O(1)$，完美匹配栈和队列的需求
2. **无扩容抖动**：`deque`分段存储，扩容时无需搬移已有元素；`vector`扩容需重新分配整块连续内存并拷贝所有元素
3. **优于`list`**：`list`每个结点需额外存储前后指针，内存开销大，且缓存命中率极低


### 5. 性能总结

| 操作 | `stack`（默认deque） | `queue`（默认deque） |
|------|---------------------|---------------------|
| 入栈/入队 | $O(1)$ | $O(1)$ |
| 出栈/出队 | $O(1)$ | $O(1)$ |
| 访问栈顶/队头 | $O(1)$ | $O(1)$ |
| 访问队尾 | — | $O(1)$ |
| 遍历 | ❌ 不支持 | ❌ 不支持 |
| 内存扩容 | 稳定（无整体搬移） | 稳定（无整体搬移） |


### 6. stack与queue的对比

| 对比项 | `stack` | `queue` |
|--------|---------|---------|
| 特性 | **LIFO**（后进先出） | **FIFO**（先进先出） |
| 默认底层容器 | `deque<T>` | `deque<T>` |
| 可替换底层容器 | `deque`、`vector`、`list` | `deque`、`list` |
| 核心操作 | `push`、`pop`、`top` | `push`、`pop`、`front`、`back` |
| 迭代器 | 无 | 无 |
| 设计模式 | 适配器模式 | 适配器模式 |


### Obsidian 双向链接建议

- `[[deque源码剖析]]`：deque是stack和queue的默认底层容器
- `[[栈的定义概念]]`：栈的ADT定义与基本操作
- `[[队列的定义概念]]`：队列的ADT定义与基本操作
- `[[vector源码剖析]]`：对比vector与deque的扩容差异

---

如需我继续展开 **“priority_queue源码剖析”** 或 **“stack与queue的迭代器适配器”** ，随时告诉我。