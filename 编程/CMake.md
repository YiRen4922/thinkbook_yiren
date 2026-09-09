## CMake 基础概念与实战 · 考研/开发通用笔记

> CMake 是一个**跨平台的构建工具生成器**，它不直接编译代码，而是根据 `CMakeLists.txt` 文件生成对应平台的构建系统文件（如 Linux 的 Makefile、Windows 的 Visual Studio 工程）。在 C++ 项目开发中，CMake 是事实标准；在考研复试/408 上机环节，能读懂基础的 CMake 配置也会加分。


### 1. CMake 是什么？

- **全称**：Cross-platform Make（跨平台 Make）
- **定位**：**元构建工具**——它生成的是其他构建工具（如 make、ninja、IDE）所需的配置文件
- **核心文件**：每个目录下的 `CMakeLists.txt` 描述如何构建该目录下的内容
- **工作流程**：
  ```
  源码 + CMakeLists.txt  →  cmake 命令  →  构建系统文件（Makefile/VS工程）
                                            ↓
                                          make / 编译
                                            ↓
                                          可执行文件/库
  ```


### 2. 为什么需要 CMake？

| 问题 | CMake 的解决方案 |
|------|------------------|
| 不同编译器/平台需要不同构建脚本 | 同一份 `CMakeLists.txt` 可生成各平台构建文件 |
| 手动写 Makefile 复杂且易错 | 用简洁的 CMake 语法描述，自动生成 Makefile |
| 大型项目依赖管理混乱 | 支持 `find_package` 自动寻找第三方库 |
| IDE 导入项目困难 | 可直接生成 VS/Xcode/CLion 工程文件 |


### 3. 核心概念

#### 3.1 Target（目标）

构建的产物，主要有三种：

| 目标类型      | CMake 命令                      | 产出                            |
| --------- | ----------------------------- | ----------------------------- |
| **可执行文件** | `add_executable()`            | `.exe` / 无后缀可执行文件             |
| **静态库**   | `add_library(XXX STATIC ...)` | `.a`（Linux）/ `.lib`（Windows）  |
| **动态库**   | `add_library(XXX SHARED ...)` | `.so`（Linux）/ `.dll`（Windows） |

#### 3.2 变量

CMake 中变量用 `${VAR}` 引用，用于存储路径、编译选项、源文件列表等。

```cmake
set(SOURCES main.cpp util.cpp)      # 设置变量
set(CMAKE_CXX_STANDARD 17)          # 设置 C++ 标准
message("Sources: ${SOURCES}")      # 打印变量
```

#### 3.3 命令（Command）

CMake 的指令，如 `project()`、`add_executable()`、`target_link_libraries()` 等，大小写不敏感（通常用小写）。

```cmake
project(MyProject)                 # 设置项目名称
add_executable(my_app main.cpp)    # 添加可执行目标
```

#### 3.4 生成器表达式（Generator Expression）

用于在生成构建系统时进行条件判断，格式为 `$<...>`。

```cmake
target_compile_definitions(my_app PRIVATE $<CONFIG:Debug>:DEBUG_MODE>)
# Debug 模式下定义 DEBUG_MODE 宏
```

#### 3.5 缓存变量（Cache Variable）

`CMakeCache.txt` 中存储的变量，跨配置持久化，通常用 `CACHE` 关键字定义用户可配置的选项。

```cmake
option(BUILD_TESTS "Build tests" ON)  # 缓存变量，用户可在 GUI 中开关
```


### 4. 常用命令速查

| 命令                                    | 用途              | 示例                                                  |
| ------------------------------------- | --------------- | --------------------------------------------------- |
| `cmake_minimum_required(VERSION ...)` | 指定所需 CMake 最低版本 | `cmake_minimum_required(VERSION 3.10)`              |
| `project(...)`                        | 设置项目名称，可选语言     | `project(MyProject LANGUAGES CXX)`                  |
| `add_executable(...)`                 | 添加可执行目标         | `add_executable(app main.cpp)`                      |
| `add_library(...)`                    | 添加库目标           | `add_library(mylib STATIC lib.cpp)`                 |
| `target_link_libraries(...)`          | 链接库到目标          | `target_link_libraries(app PRIVATE mylib)`          |
| `target_include_directories(...)`     | 添加头文件搜索路径       | `target_include_directories(app PRIVATE include/)`  |
| `target_compile_definitions(...)`     | 添加宏定义           | `target_compile_definitions(app PRIVATE VERSION=1)` |
| `target_compile_features(...)`        | 指定 C++ 特性要求     | `target_compile_features(app PRIVATE cxx_std_17)`   |
| `find_package(...)`                   | 查找外部包           | `find_package(OpenCV REQUIRED)`                     |
| `add_subdirectory(...)`               | 添加子目录           | `add_subdirectory(src)`                             |
| `include_directories(...)`            | 全局头文件路径（不推荐）    | 优先用 `target_include_directories`                    |
| `link_directories(...)`               | 全局链接路径（不推荐）     | 优先用 `target_link_directories`                       |
| `aux_source_directory(...)`           | 收集目录下所有源文件      | `aux_source_directory(src SOURCES)`                 |


### 5. 变量/属性作用域

理解 CMake 的作用域规则是避免混乱的关键：

| 作用域类型         | 说明                              | 行为                           |
| ------------- | ------------------------------- | ---------------------------- |
| **Directory** | 当前 `CMakeLists.txt` 及其子目录       | 全局变量跨目录可见，但子目录可覆盖            |
| **Target**    | 每个 target 独立的属性（头文件路径、编译选项、链接库） | 通过 `target_*` 命令设置，互不影响      |
| **Function**  | 函数内部                            | 函数内变量默认本地，除非用 `PARENT_SCOPE` |
| **Cache**     | 缓存变量 `CMakeCache.txt`           | 跨配置持久化，全局可见                  |

> [!important] 核心原则
> **现代 CMake 推荐用 `target_*` 命令而非全局命令**（如 `include_directories` 已被 `target_include_directories` 替代），因为 target 级别的设置更精确、不污染其他目标。


### 6. CMake 常用变量

| 变量                         | 含义                                 |
| -------------------------- | ---------------------------------- |
| `CMAKE_SOURCE_DIR`         | 顶层 CMakeLists.txt 所在目录             |
| `CMAKE_BINARY_DIR`         | 构建输出根目录                            |
| `CMAKE_CURRENT_SOURCE_DIR` | 当前 CMakeLists.txt 所在目录             |
| `CMAKE_CURRENT_BINARY_DIR` | 当前构建目录                             |
| `CMAKE_CXX_STANDARD`       | C++ 标准版本（11/14/17/20）              |
| `CMAKE_BUILD_TYPE`         | 构建类型（Debug/Release/RelWithDebInfo） |
| `PROJECT_NAME`             | 当前项目名称                             |
| `PROJECT_SOURCE_DIR`       | 项目根目录                              |
| `PROJECT_BINARY_DIR`       | 项目构建根目录                            |
| `CMAKE_INSTALL_PREFIX`     | 安装路径前缀                             |


### 7. 应用示例一：单个可执行文件

**目录结构**：
```
my_project/
├── CMakeLists.txt
├── src/
│   └── main.cpp
└── include/
    └── mylib.h
```

**CMakeLists.txt**：
```cmake
cmake_minimum_required(VERSION 3.10)          # 1. 指定 CMake 最低版本
project(MyApp VERSION 1.0 LANGUAGES CXX)      # 2. 项目名称、版本、语言

set(CMAKE_CXX_STANDARD 17)                    # 3. 使用 C++17
set(CMAKE_CXX_STANDARD_REQUIRED ON)           # 4. 强制要求
set(CMAKE_CXX_EXTENSIONS OFF)                 # 5. 禁用 GNU 扩展

add_executable(my_app src/main.cpp)           # 6. 添加可执行目标

target_include_directories(my_app PRIVATE include/)  # 7. 头文件路径
```

**编译**：
```bash
mkdir build && cd build
cmake ..      # 生成 Makefile
make          # 编译 → 生成 my_app
```


### 8. 应用示例二：库 + 链接库的多目标项目

**目录结构**：
```
my_lib_project/
├── CMakeLists.txt
├── src/
│   ├── CMakeLists.txt          # 子目录
│   ├── math_utils.cpp
│   └── string_utils.cpp
├── include/
│   ├── math_utils.h
│   └── string_utils.h
└── app/
    ├── CMakeLists.txt          # 子目录
    └── main.cpp
```

**顶层 CMakeLists.txt**：
```cmake
cmake_minimum_required(VERSION 3.10)
project(MyLibProject LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 可选：Debug/Release 模式切换
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release)
endif()

# 添加子目录
add_subdirectory(src)
add_subdirectory(app)
```

**src/CMakeLists.txt**（构建库）：
```cmake
# 收集源文件
set(SOURCES
    math_utils.cpp
    string_utils.cpp
)

# 创建静态库
add_library(my_utils STATIC ${SOURCES})

# 头文件目录（PUBLIC：依赖该库的目标也能访问）
target_include_directories(my_utils
    PUBLIC
        ${CMAKE_SOURCE_DIR}/include
    PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}
)

# 设置库的属性（可选）
set_target_properties(my_utils PROPERTIES
    VERSION 1.0.0
    SOVERSION 1
)
```

**app/CMakeLists.txt**（可执行文件）：
```cmake
add_executable(my_app main.cpp)

# 链接库
target_link_libraries(my_app
    PRIVATE
        my_utils
)

# 头文件路径（依赖库的头文件通过 PUBLIC 传播，无需再设置）
```

**编译**：
```bash
mkdir build && cd build
cmake ..
make
./my_app
```


### 9. 408 关联与考研场景

| 考研场景 | CMake 的关联 |
|----------|-------------|
| **复试机试环境** | 部分院校机试要求用 CMake 管理项目 |
| **408 上机准备** | 理解 `CMakeLists.txt` 的基本结构有助于快速搭建调试环境 |
| **C++ 项目开发** | 复试面试可能被问到“C++ 项目如何管理” |
| **开源项目阅读** | 多数开源 C++ 项目用 CMake，能看懂 `CMakeLists.txt` 有助于理解项目结构 |

> 考研初试不考 CMake，但**复试阶段**如果你能在简历上写“熟悉 CMake”，并在面试中简单解释其作用，会给老师留下“工程能力强”的印象。


### Obsidian 双向链接建议

- `[[C++编译与链接]]`：CMake 管理的是编译链接过程
- `[[Linux开发环境]]`：CMake 在 Linux 下的使用
- `[[Git与版本控制]]`：项目源码管理
- `[[数据结构项目构建]]`：为 408 算法题搭建可复用的测试框架

---

如需我继续展开 **“CMake 的常见错误与调试方法”** 或 **“find_package 的详细机制”** ，随时告诉我。