# typedef 用法（通俗版）

> [!tip] 一句话理解
> `typedef` 就是**给类型起外号**。  
> 原来的类型名字太长、太难看、太难记，就给它起个简短好记的名字。

---

## 1. 最基本的用法

```cpp
typedef 原类型  新名字;
```

### 例子1：给基本类型起外号
```cpp
typedef unsigned long ulong;      // 把 unsigned long 叫做 ulong
typedef long long ll;             // 竞赛党最爱

ulong a = 100;
ll b = 1234567890123;
```

### 例子2：给结构体起外号（最常见）
```cpp
struct Student {
    int id;
    char name[20];
};

// 以前每次都要写 struct Student
struct Student s1;

// 用 typedef 后：
typedef struct Student Student;   // 或者直接写在一起

Student s2;   // 干净多了
```

也可以直接写在一起：
```cpp
typedef struct {
    int id;
    char name[20];
} Student;

Student s;
```

---

## 2. 实际中最有用的场景

### 场景A：复杂类型简化
```cpp
// 原始写法（看得想死）
std::map<std::string, std::vector<std::pair<int, double>>> data;

// 用 typedef 起个名字
typedef std::map<std::string, std::vector<std::pair<int, double>>> ScoreMap;

ScoreMap data;   // 瞬间清爽
```

### 场景B：函数指针（经典难点）
函数指针本身就很难写，用 typedef 能救命：

```cpp
// 原始函数指针（劝退）
int (*p)(int, int);

// 用 typedef
typedef int (*FuncPtr)(int, int);

FuncPtr p = add;   // 清晰很多
```

### 场景C：数组类型
```cpp
typedef int IntArray[10];

IntArray arr;     // 等价于 int arr[10];
```

---

## 3. 现代 C++ 更推荐用 `using`（C++11）

从 C++11 开始，官方更推荐用 `using`，因为它更强、更清晰：

```cpp
// typedef 写法
typedef std::vector<int> IntVec;

// 现代 using 写法（推荐）
using IntVec = std::vector<int>;
```

`using` 的优势：
- 支持模板别名（typedef 做不到）
- 读起来更像赋值，更符合直觉

```cpp
// using 可以这样写模板别名
template<typename T>
using Vec = std::vector<T>;

Vec<int> v;       // 等价于 std::vector<int>
Vec<double> d;
```

---

## 4. 对比总结

| 特性           | typedef          | using（C++11）     | 推荐程度     |
|----------------|------------------|--------------------|--------------|
| 基本类型别名   | 支持             | 支持               | 都行         |
| 函数指针       | 支持             | 支持               | 都行         |
| 模板别名       | **不支持**       | **支持**           | using 完胜   |
| 可读性         | 一般             | 更好               | using        |
| 老代码兼容     | 非常常见         | 新代码常用         | -            |

---

## 5. 实际建议

1. **新代码**：优先用 `using`，不要再用 typedef。
2. **看老代码 / 维护旧项目**：必须认识 typedef。
3. **给复杂类型起名字**：强烈建议起，代码可读性会好很多。
4. **函数指针**：不管新老，都建议用别名包一层。

---

## 6. 快速记忆

> typedef 就是「类型的化妆师」。  
> 把又长又丑的类型，化成简短好认的样子。  
> 但现在有了更强的化妆师叫 `using`，新妆（新代码）请用它。

---

**标签**： #CPP #typedef #using #类型别名 #基础语法 #笔记

