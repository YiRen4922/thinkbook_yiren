Make就是一个具有多级依赖的自动脚本执行程序，Makefile就是需要执行的脚本文件。

示例：
```sh
CC = gcc
CFLAGS = -Wall -O2
TARGET = hello

PREFIX ?= /usr/local
BINDIR = $(PREFIX)/bin

all: $(TARGET)

$(TARGET): hello.c
	$(CC) $(CFLAGS) -o $(TARGET) hello.c

install: $(TARGET)
	@echo "Installing $(TARGET) to $(BINDIR)"
	mkdir -p $(BINDIR)
	cp $(TARGET) $(BINDIR)/

clean:
	rm -f $(TARGET)

```

# 1. 变量（简化代码）

如果项目中多次用到 `gcc` 或 `-Wall -g`（编译选项），直接写会重复，可通过 “变量” 简化。修改前面的 Makefile，用变量优化：
```makefile
# 定义变量：变量名=值（习惯大写，区分普通字符）
CC = gcc                # 编译器（后续用 $(CC) 代替 gcc）
CFLAGS = -Wall -g       # 编译选项（-Wall 显示所有警告，-g 生成调试信息）
TARGET = main           # 目标可执行文件名
OBJS = main.o func.o    # 所有目标文件（.o）

# 第一个规则：生成可执行文件
$(TARGET): $(OBJS)
	$(CC) $(OBJS) -o $(TARGET)  # 用 $(变量名) 引用变量

# 生成 main.o：依赖 main.c 和 func.h，用 CFLAGS 选项
main.o: main.c func.h
	$(CC) $(CFLAGS) -c main.c -o main.o

# 生成 func.o
func.o: func.c func.h
	$(CC) $(CFLAGS) -c func.c -o func.o

# 伪目标：声明 clean 是伪目标（避免目录中有同名文件时冲突）
.PHONY: clean
clean:
	rm -f $(TARGET) $(OBJS)  # 用变量统一清理
```

- 变量的优势：如果后续要换编译器（如 `clang`），只需改 `CC = clang`，不用改所有命令；添加编译选项也只需改 `CFLAGS`。
- `.PHONY: clean`：声明 `clean` 是 “伪目标”，告诉 `make`：即使目录中有 `clean` 文件，也执行 `clean` 目标下的命令（避免冲突）。
# 2. 规则三要素（核心）
每个规则的格式固定：
```makefile
目标（target）: 依赖（prerequisites）
	命令（commands）  # 必须以 Tab 开头！（新手最易踩的坑）
```

  目标代表需要输出的文件，依赖代表需要存在的文件，命令代表满足依赖时执行的命令，用于生成目标文件

- **目标**：要生成的文件（如 `main`、`main.o`），也可以是 “伪目标”（如 `clean`，无实际文件，仅用于执行命令）。
- **依赖**：生成目标必须的文件（如 `main` 依赖 `main.o` 和 `func.o`）。
- **命令**：从依赖生成目标的具体操作（如 `gcc` 命令），每一行命令前必须是 **Tab 键**（空格不行，Makefile 语法强制要求）。

多级以来，如果某个生成规则的依赖不存在，那么：
- 情况 1：存在生成该依赖的规则 → 自动生成依赖
- 情况 2：不存在生成该依赖的规则 → 报错终止
这个自动生成依赖的过程理论上可以多级一直调用生成规则。

# 常见伪目标列表

### 1. 基本构建目标

makefile

.PHONY: all
all: program1 program2

- **作用**：默认目标，通常用于构建所有内容
    
- **用法**：`make` 或 `make all`
    

### 2. 清理目标

makefile

.PHONY: clean
clean:
    rm -f *.o core *.core program

- **作用**：删除所有编译生成的文件
    
- **用法**：`make clean`
    

### 3. 安装目标

makefile

.PHONY: install
install: program
    install -m 755 program /usr/local/bin/

- **作用**：将编译好的程序安装到系统目录
    
- **用法**：`make install`
    

### 4. 卸载目标

makefile

.PHONY: uninstall
uninstall:
    rm -f /usr/local/bin/program

- **作用**：从系统中移除已安装的程序
    
- **用法**：`make uninstall`
    

### 5. 测试目标

makefile

.PHONY: test check
test: program
    ./test-suite.sh

- **作用**：运行测试套件
    
- **用法**：`make test` 或 `make check`
    

### 6. 打包目标

makefile

.PHONY: dist
dist:
    tar -czf project-1.0.tar.gz src/ include/ Makefile README

- **作用**：创建源代码分发包
    
- **用法**：`make dist`
    

### 7. 配置目标

makefile

.PHONY: config configure
config:
    ./configure --prefix=/usr/local

- **作用**：配置构建环境（常见于 Autotools 项目）
    
- **用法**：`make config`
    

### 8. 文档目标

makefile

.PHONY: docs
docs:
    doxygen Doxyfile

- **作用**：生成项目文档
    
- **用法**：`make docs`
    

### 9. 依赖检查目标

makefile

.PHONY: dep depend
depend:
    makedepend -Y -- $(CFLAGS) -- $(SRCS)

- **作用**：生成或更新依赖关系
    
- **用法**：`make depend`
    

### 10. 显示帮助目标

makefile

.PHONY: help
help:
    @echo "可用目标:"
    @echo "  all    构建所有内容"
    @echo "  clean  清理生成的文件"
    @echo "  install 安装程序"
    @echo "  test   运行测试"

- **作用**：显示可用目标和简要说明
    
- **用法**：`make help`
    


# 变量定义
CC = gcc
CFLAGS = -Wall -g
TARGET = myapp
SRCS = main.c utils.c
OBJS = $(SRCS:.c=.o)

# 默认目标
.PHONY: all
all: $(TARGET)

# 链接目标
$(TARGET): $(OBJS)
    $(CC) $(CFLAGS) -o $@ $^

# 编译规则
%.o: %.c
    $(CC) $(CFLAGS) -c $< -o $@

# 清理
.PHONY: clean
clean:
    rm -f $(TARGET) $(OBJS)

# 安装
.PHONY: install
install: $(TARGET)
    install -m 755 $(TARGET) /usr/local/bin/

# 卸载
.PHONY: uninstall
uninstall:
    rm -f /usr/local/bin/$(TARGET)

# 测试
.PHONY: test
test: $(TARGET)
    ./test_runner.sh

# 显示帮助
.PHONY: help
help:
    @echo "可用目标:"
    @echo "  make all      构建程序 (默认)"
    @echo "  make clean    清理生成的文件"
    @echo "  make install  安装到系统"
    @echo "  make uninstall 从系统移除"
    @echo "  make test     运行测试"

## 使用建议

1. **总是声明伪目标**：使用 `.PHONY` 明确声明所有不生成文件的目标
    
2. **保持一致性**：在整个项目中使用一致的伪目标名称
    
3. **提供帮助**：包含 `help` 目标，方便用户了解可用选项
    
4. **考虑标准**：遵循 GNU 编码标准中关于目标命名的建议
    

这些常见的伪目标使得 Makefile 更加标准化和易于使用，用户可以通过简单的 `make target` 命令执行各种构建和管理任务。