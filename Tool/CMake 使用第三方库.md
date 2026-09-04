## CMake 项目中使用第三方库的完整指南

> 在真实 C++ 项目中，几乎不可能不用到第三方库。理解如何将别人的库引入自己的项目，是区分“会写代码”和“能写工程”的关键分水岭。以下用两个真实开源库（**spdlog** 和 **fmt**）举例，讲清楚使用他人源码的完整流程。


### 1. 使用第三方库的两种方式

| 方式 | 适用场景 | 优点 | 缺点 |
|------|----------|------|------|
| **内部构建**（源码嵌入） | 库较小、无外部依赖、你想随项目一起编译 | 版本可控、无需安装、跨平台简单 | 增大项目体积、编译变慢 |
| **外部构建**（find_package） | 库已安装在系统或工具链中 | 编译快、复用系统库 | 依赖系统环境、版本管理复杂 |


### 2. 方式一：内部构建（源码嵌入）

**核心思想**：将第三方库的源码放入你的项目，用 `add_subdirectory()` 将其纳入构建流程。

#### 2.1 项目结构

```
my_project/
├── CMakeLists.txt              # 顶层
├── src/
│   └── main.cpp
└── third_party/                # 第三方库统一放这里
    └── spdlog/                 # GitHub 上的 spdlog 源码
        ├── CMakeLists.txt
        ├── include/
        └── src/
```

#### 2.2 顶层 CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.10)
project(MyApp LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# ============================================
# 引入第三方库：spdlog（只包含头文件）
# ============================================
add_subdirectory(third_party/spdlog)

# ============================================
# 你的应用
# ============================================
add_executable(my_app src/main.cpp)

# 链接 spdlog 库（即使它是 header-only，也需要 target_link_libraries 来传递依赖）
target_link_libraries(my_app PRIVATE spdlog::spdlog)

# 如果你不想用 target_link_libraries，也可以直接添加包含路径（不推荐）：
# target_include_directories(my_app PRIVATE third_party/spdlog/include)
```

#### 2.3 源码中使用

```cpp
// src/main.cpp
#include <spdlog/spdlog.h>

int main() {
    spdlog::info("Hello, {}!", "world");
    return 0;
}
```

#### 2.4 编译命令

```bash
mkdir build && cd build
cmake ..
make
./my_app   # 输出: [info] Hello, world!
```

> **优点**：无需预先安装库，`git clone` 后直接编译
> **缺点**：每次编译都要重新编译 spdlog（虽然 header-only 影响不大）


### 3. 方式二：外部构建（find_package）

**核心思想**：库已安装在系统中，用 `find_package()` 寻找并使用它。

#### 3.1 安装第三方库（以 fmt 为例）

```bash
# 下载 fmt 源码
git clone https://github.com/fmtlib/fmt.git
cd fmt

# 构建并安装到系统目录（Linux/macOS）
mkdir build && cd build
cmake .. -DCMAKE_INSTALL_PREFIX=/usr/local
make
sudo make install

# 安装后，fmt 会在 /usr/local/lib/cmake/fmt/ 下生成 fmt-config.cmake
```

#### 3.2 项目结构

```
my_project/
├── CMakeLists.txt
└── src/
    └── main.cpp
```

#### 3.3 CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.10)
project(MyApp LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# ============================================
# 查找已安装的 fmt 库
# ============================================
find_package(fmt REQUIRED)

# 如果 fmt 没有提供 CMake 配置文件，可以手动指定：
# find_package(fmt REQUIRED PATHS /usr/local/lib/cmake/fmt)

# ============================================
# 你的应用
# ============================================
add_executable(my_app src/main.cpp)

# 链接 fmt（用 fmt::fmt 或 fmt::fmt-header-only）
target_link_libraries(my_app PRIVATE fmt::fmt)
```

#### 3.4 源码中使用

```cpp
// src/main.cpp
#include <fmt/core.h>

int main() {
    fmt::print("Hello, {}!\n", "world");
    return 0;
}
```

#### 3.5 编译命令

```bash
mkdir build && cd build
cmake ..
make
./my_app   # 输出: Hello, world!
```


### 4. 两种方式的对比与选择

| 对比项 | 内部构建 (add_subdirectory) | 外部构建 (find_package) |
|--------|---------------------------|------------------------|
| **库位置** | 在项目目录内 | 系统已安装 |
| **版本控制** | 由你的 Git 仓库管理 | 依赖系统版本，可能不一致 |
| **首次编译** | 较慢（需编译库） | 快（库已编译好） |
| **移植性** | 强（源码随项目走） | 弱（每台机器需安装） |
| **团队协作** | 简单（clone 即用） | 复杂（需统一环境） |
| **推荐场景** | 小团队、嵌入式、长期维护 | 大项目、CI/CD、系统库 |


### 5. 推荐做法：支持两种方式并存的写法

```cmake
# 优先找系统安装的库，找不到则使用源码
find_package(fmt QUIET)

if(NOT fmt_FOUND)
    message(STATUS "fmt not found, building from source")
    add_subdirectory(third_party/fmt)
else()
    message(STATUS "fmt found in system")
endif()

add_executable(my_app src/main.cpp)
target_link_libraries(my_app PRIVATE fmt::fmt)
```


### 6. 核心概念详解

#### 6.1 什么是 `find_package`？

`find_package` 是 CMake 提供的**包查找机制**。它会寻找两种文件：

| 查找方式 | 文件类型 | 优先级 | 说明 |
|----------|----------|--------|------|
| **Config 模式** | `*-config.cmake` | 高 | 库作者提供的官方配置（推荐） |
| **Module 模式** | `Find*.cmake` | 低 | CMake 内置或社区提供的查找脚本 |

```cmake
# Config 模式（推荐）
find_package(fmt REQUIRED)           # 找 fmt-config.cmake

# Module 模式（备选）
find_package(OpenCV REQUIRED)        # 找 FindOpenCV.cmake
```

#### 6.2 `find_package` 找到的是什么？

`find_package` 成功后，会设置一系列变量，并提供**导入目标（Imported Target）**：

| 变量/目标 | 含义 | 示例值 |
|-----------|------|--------|
| `PACKAGE_FOUND` | 是否找到 | `TRUE` |
| `PACKAGE_INCLUDE_DIRS` | 头文件路径 | `/usr/local/include` |
| `PACKAGE_LIBRARIES` | 库文件路径 | `/usr/local/lib/libfmt.a` |
| `PACKAGE::PACKAGE` | **导入目标**（推荐用法） | `fmt::fmt` |

```cmake
# 使用导入目标（最干净的方式）
target_link_libraries(my_app PRIVATE fmt::fmt)

# 或者手动使用变量（不推荐）
target_include_directories(my_app PRIVATE ${fmt_INCLUDE_DIRS})
target_link_libraries(my_app PRIVATE ${fmt_LIBRARIES})
```

#### 6.3 `add_subdirectory` 发生了什么？

`add_subdirectory(third_party/spdlog)` 会执行该目录下的 `CMakeLists.txt`，从而在**当前构建作用域**中定义新的 target（如 `spdlog::spdlog`）。之后 `target_link_libraries` 就能像链接自己项目内的 target 一样链接它。

#### 6.4 `target_link_libraries` 的三个作用域（PUBLIC/PRIVATE/INTERFACE）

| 作用域 | 含义 | 依赖传递 |
|--------|------|----------|
| `PRIVATE` | 仅供当前目标内部使用 | **不传递**给依赖者 |
| `PUBLIC` | 当前目标内部使用，且依赖者也用 | **传递**给依赖者 |
| `INTERFACE` | 当前目标内部不用，仅供依赖者使用 | **传递**给依赖者 |

```cmake
# 示例：my_utils 是一个库
target_include_directories(my_utils PUBLIC include/)   # 依赖者也看到
target_link_libraries(my_utils PRIVATE fmt::fmt)       # 仅 my_utils 内部用 fmt

# 当 app 链接 my_utils 时，app 会自动获得 my_utils 的 PUBLIC 头文件
# 但不会自动链接 fmt（因为 fmt 是 PRIVATE）
target_link_libraries(app PRIVATE my_utils)
```

#### 6.5 安装前缀与库查找路径

在 Linux/macOS 上，`find_package` 默认会搜索：

```
/usr/local/lib/cmake/   # 本地安装
/usr/lib/cmake/         # 系统安装
/opt/*/lib/cmake/       # 第三方工具链
```

你可以通过 `CMAKE_PREFIX_PATH` 来添加自定义搜索路径：

```bash
cmake .. -DCMAKE_PREFIX_PATH=/home/user/libs
```


### 7. 常见问题与解决方案

#### 7.1 `find_package` 找不到库

**原因**：库未安装、或安装路径不在 CMake 搜索路径中。

**解决方案**：

```cmake
# 方法1：手动指定路径
find_package(fmt REQUIRED PATHS /usr/local/lib/cmake/fmt)

# 方法2：设置环境变量（推荐）
export CMAKE_PREFIX_PATH=/opt/my_libs:$CMAKE_PREFIX_PATH
```

#### 7.2 库版本冲突

**解决方案**：使用 `find_package(XXX VERSION 1.2.3 REQUIRED)` 指定版本。

#### 7.3 找不到头文件（`No such file or directory`）

**原因**：库的头文件没有正确添加到包含路径。

**解决方案**：

```cmake
# 排查：检查目标是否包含 include 路径
get_target_property(inc_dirs fmt::fmt INTERFACE_INCLUDE_DIRECTORIES)
message(STATUS "fmt include dirs: ${inc_dirs}")

# 手动补加
target_include_directories(my_app PRIVATE /path/to/include)
```


### 8. 完整示例：使用 spdlog 和 fmt 的项目

```cmake
cmake_minimum_required(VERSION 3.10)
project(LoggingApp LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 引入 spdlog（header-only）
add_subdirectory(third_party/spdlog)

# 寻找 fmt（优先用系统，否则源码）
find_package(fmt QUIET)
if(NOT fmt_FOUND)
    add_subdirectory(third_party/fmt)
endif()

# 你的应用
add_executable(logger src/main.cpp)

# 链接
target_link_libraries(logger PRIVATE spdlog::spdlog fmt::fmt)

# 如果 fmt 是源码构建，可能需要额外包含路径
# （spdlog 会自动处理，无需手动添加）
```


### Obsidian 双向链接建议

- `[[CMake基础概念]]`：CMake 的入门知识
- `[[C++编译与链接]]`：库是如何被链接的
- `[[Git与版本控制]]`：如何管理第三方库的版本（git submodule）
- `[[vcpkg与包管理]]`：现代 C++ 的包管理方案

---

如需我继续展开 **“vcpkg 与 CMake 集成”** 或 **“Git Submodule 管理第三方库”**，随时告诉我。