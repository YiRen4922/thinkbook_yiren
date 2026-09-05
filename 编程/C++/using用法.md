# using 用法（通俗版）

> [!tip] 一句话理解
> `using` 是 C++11 引入的**类型别名**新写法，用来给类型起外号。  
> 它是 `typedef` 的升级版，功能更强、写法更清晰。

---

## 1. 基本用法

```cpp
using 新名字 = 原类型;
```

### 例子1：给普通类型起别名
```cpp
using ull = unsigned long long;
using ll  = long long;

ull a = 100;
ll b = -123456789;
```

### 例子2：给复杂类型起别名（最常用）
```cpp
using ScoreMap = std::map<std::string, std::vector<int>>;

ScoreMap data;   // 干净又好读
```

### 例子3：给结构体 / 类起别名
```cpp
struct Point {
    int x, y;
};

using Pt = Point;
Pt p{10, 20};
```

---

## 2. using 比 typedef 强在哪里？

### 核心优势：支持**模板别名**（typedef 做不到）

```cpp
// 正确示范：using 可以给模板起别名
template<typename T>
using Vec = std::vector<T>;

Vec<int>    vi;     // 等价于 std::vector<int>
Vec<double> vd;     // 等价于 std::vector<double>
Vec<std::string> vs;
```

```cpp
// typedef 想这样做？做不到！
template<typename T>
typedef std::vector<T> Vec;   // 编译错误
```

### 其他优势
- 读起来更像“赋值”，更符合直觉
- 可以写在函数内部、命名空间内，作用域更灵活
- 语法和 `using namespace` 统一，不容易混淆

---

## 3. 常见实用场景

### 场景A：简化超长类型
```cpp
using Iter = std::vector<int>::iterator;
using ConstIter = std::vector<int>::const_iterator;

Iter it = v.begin();
```

### 场景B：函数指针 / 函数类型
```cpp
// 函数指针
using FuncPtr = int(*)(int, int);

// 函数类型（C++11 起更常用）
using FuncType = int(int, int);

FuncPtr p = add;
```

### 场景C：配合智能指针
```cpp
using IntPtr    = std::unique_ptr<int>;
using SharedStr = std::shared_ptr<std::string>;

IntPtr p = std::make_unique<int>(42);
```

### 场景D：嵌套类型简化
```cpp
using Map = std::unordered_map<std::string, int>;
using MapIter = Map::iterator;
```

---

## 4. using 的其他用法（顺便了解）

`using` 不止能起类型别名，还有两个常见用途：

### 1. 引入命名空间成员
```cpp
using std::cout;
using std::endl;

cout << "hello" << endl;   // 不用写 std::
```

### 2. 继承构造函数 / 基类成员（进阶）
```cpp
class Base {
public:
    Base(int x);
};

class Derived : public Base {
public:
    using Base::Base;   // 继承基类构造函数（C++11）
};
```

> 注意：日常写代码时，**类型别名**是 `using` 最常用的功能。

---

## 5. typedef vs using 快速对比

| 项目         | typedef              | using（推荐）          |
|--------------|----------------------|------------------------|
| 出现时间     | C 语言就有           | C++11 引入             |
| 基本别名     | 支持                 | 支持                   |
| 模板别名     | **不支持**           | **支持**               |
| 可读性       | 一般                 | 更好（像赋值）         |
| 推荐程度     | 只在维护老代码时用   | **新代码首选**         |

---

## 6. 实际建议

1. **新代码全部用 `using`**，不要再用 `typedef`。
2. 看到复杂类型（尤其是带模板的），果断起别名。
3. 别名起名要有意义，比如 `UserMap`、`TaskPtr`、`Callback`，不要乱起。
4. 可以在类内部、函数内部使用，缩小作用域，更安全。

---

## 7. 快速记忆

> `using` = 现代版 typedef。  
> 写法更像赋值：`using 新名字 = 老类型;`  
> 最大杀器是**支持模板别名**。  
> 新代码请无脑用 `using`。

---

**标签**： #CPP #using #类型别名 #现代CPP #基础语法 #笔记
