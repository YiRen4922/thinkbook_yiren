# 现代 C++ 笔记

> [!abstract] 概述
> 现代 C++ 通常指 **C++11 及以后** 的版本（C++11/14/17/20/23）。它在保持高性能的同时，大幅提升了安全性、表达力和开发效率。本笔记重点整理现代 C++ 的核心特性与标准库（尤其是 STL）的现代化内容。

---

## 1. 现代 C++ 版本演进

| 版本     | 发布时间 | 标志性特性 |
|----------|----------|------------|
| **C++11** | 2011    | 移动语义、右值引用、Lambda、`auto`、智能指针、`nullptr`、范围 for |
| **C++14** | 2014    | 泛型 Lambda、变量模板、`make_unique`、二进制字面量 |
| **C++17** | 2017    | 结构化绑定、`if constexpr`、`std::optional` / `std::variant` / `std::any`、文件系统库、并行算法 |
| **C++20** | 2020    | Concepts、Ranges、协程、Modules、三路比较（`<=>`）、`std::span` |
| **C++23** | 2023    | `std::expected`、`std::mdspan`、`std::print`、更多 Ranges 改进、`std::flat_map` 等 |

---

## 2. 现代 C++ 核心特性速览

### 2.1 类型推导与简洁语法
- `auto` 与 `decltype`
- 范围 for 循环：`for (auto& x : container)`
- 结构化绑定（C++17）：`auto [a, b] = pair;`
- 初始化列表与统一初始化

### 2.2 移动语义与完美转发
- 右值引用 `T&&`
- 移动构造函数 / 移动赋值运算符
- `std::move`、`std::forward`
- 大幅减少不必要的拷贝

### 2.3 Lambda 表达式
```cpp
auto add = [](int a, int b) { return a + b; };
auto generic = [](auto x, auto y) { return x + y; };  // C++14 泛型 Lambda
```

### 2.4 智能指针（几乎替代裸指针）
- `std::unique_ptr`：独占所有权
- `std::shared_ptr`：共享所有权
- `std::weak_ptr`：打破循环引用
- 推荐优先使用 `std::make_unique` / `std::make_shared`

### 2.5 其他重要特性
- `nullptr`（替代 `NULL`）
- `constexpr` 与编译期计算
- `noexcept`
- 委托构造函数、继承构造函数
- 强类型枚举 `enum class`
- Attributes：`[[nodiscard]]`、`[[maybe_unused]]`、`[[deprecated]]`

---

## 3. 现代 STL 结构

现代 C++ 中，传统 STL（容器 + 算法 + 迭代器）依然是核心，但已经全面现代化。

### 3.1 容器（Containers）

**序列容器**
- `std::vector`（最常用）
- `std::array`（固定大小，栈上）
- `std::deque`
- `std::list` / `std::forward_list`

**关联容器**
- 有序：`std::map` / `std::set` / `std::multimap` / `std::multiset`（红黑树）
- 无序：`std::unordered_map` / `std::unordered_set` 等（哈希表，C++11）

**容器适配器**
- `std::stack`、`std::queue`、`std::priority_queue`

**现代推荐**
- 优先使用 `emplace_back` / `emplace` 而非 `push_back`
- 能用 `std::array` 就不用 C 风格数组
- 需要快速查找优先考虑 `unordered_*`

### 3.2 算法（Algorithms）
- 头文件：`<algorithm>`、`<numeric>`
- C++17 起支持**并行算法**（`std::execution::par`）
- C++20 起强烈推荐使用 **Ranges** 版本

```cpp
#include <ranges>
#include <algorithm>
#include <vector>

std::vector<int> v = {5, 3, 1, 4, 2};
std::ranges::sort(v);                    // C++20
auto even = v | std::views::filter([](int x){ return x % 2 == 0; });
```

### 3.3 迭代器与 Ranges（C++20 重大升级）
- 传统五种迭代器分类仍然存在
- **Ranges** 让算法可以直接接受容器，并支持惰性视图（Views）和管道语法 `|`

### 3.4 函数对象与可调用对象
- 传统仿函数（`std::less<>` 等）
- Lambda（现代首选）
- `std::function`、`std::bind`、`std::invoke`
- C++20 的 `std::bind_front`

---

## 4. 现代 C++ 常用标准库组件

| 头文件          | 用途                     | 关键组件 |
|-----------------|--------------------------|----------|
| `<memory>`      | 智能指针与内存管理       | unique_ptr, shared_ptr, make_unique |
| `<utility>`     | 通用工具                 | pair, move, forward, exchange |
| `<optional>`    | 可选值（C++17）          | std::optional |
| `<variant>`     | 类型安全的联合体（C++17）| std::variant |
| `<any>`         | 任意类型（C++17）        | std::any |
| `<string_view>` | 只读字符串视图（C++17）  | std::string_view |
| `<span>`        | 连续内存视图（C++20）    | std::span |
| `<chrono>`      | 时间与日期               | duration, time_point, steady_clock |
| `<random>`      | 高质量随机数             | mt19937, uniform_int_distribution |
| `<filesystem>`  | 文件系统操作（C++17）    | path, directory_iterator |
| `<ranges>`      | 范围库（C++20）          | views, ranges::sort 等 |
| `<concepts>`    | 概念约束（C++20）        | std::integral, std::sortable 等 |

---

## 5. 现代 C++ 最佳实践速记

1. **优先使用标准库**，避免自己造轮子（尤其是容器、智能指针、算法）。
2. **优先值语义 + 移动**，减少不必要的指针和手动内存管理。
3. **能用 `auto` 就用 `auto`**（尤其是迭代器和复杂类型）。
4. **范围 for + 结构化绑定** 让代码更清晰。
5. **Lambda 优先于手写函数对象**。
6. **C++20 起尽量使用 Ranges + Concepts**。
7. **编译期能做的事情尽量用 `constexpr` / `consteval` 做掉**。
8. **使用 `[[nodiscard]]` 标记重要返回值**。
9. **异常安全**：优先使用 RAII（智能指针、锁守卫等）。
10. **代码风格**：遵循 [C++ Core Guidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines)

---

## 6. 推荐学习资源

- **书籍**
  - 《Effective Modern C++》（Scott Meyers）
  - 《C++ Primer》（第5版）
  - 《A Tour of C++》（Bjarne Stroustrup）
  - 《C++20 - The Complete Guide》（Nicolai Josuttis）

- **在线**
  - [cppreference.com](https://en.cppreference.com)（最权威参考）
  - [C++ Core Guidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines)
  - Compiler Explorer（godbolt.org）

---

## 7. 快速记忆口诀

> 现代 C++ 三板斧：  
> **移动语义** 减少拷贝，  
> **智能指针** 管理资源，  
> **Lambda + auto** 让代码简洁。  
>  
> C++20 后再加两样：  
> **Ranges** 管道式编程，  
> **Concepts** 约束模板更清晰。

---

**标签**： #CPP #现代CPP #STL #CPP11 #CPP17 #CPP20 #笔记
```

把上面整段内容直接复制到 Obsidian 即可使用。需要我针对某一部分（比如 Ranges、智能指针、容器选型）再单独写一页更详细的笔记吗？