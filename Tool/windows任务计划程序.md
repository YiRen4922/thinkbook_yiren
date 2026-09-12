# Windows 任务计划程序设置步骤

以 OmniRoute 开机自启为例。

## 1. 打开任务计划程序

按：

```text
Win + R
```

输入：

```text
taskschd.msc
```

回车。

## 2. 创建任务

左侧点击：

```text
任务计划程序库
```

右侧点击：

```text
创建任务
```

不要选择“创建基本任务”。

## 3. 常规

名称：

```text
OmniRoute
```

选择：

```text
☑ 仅当用户登录时运行
☑ 使用最高权限运行
```

## 4. 触发器

进入：

```text
触发器 → 新建
```

设置：

```text
开始任务：登录时
特定用户：当前用户
```

建议：

```text
☑ 延迟任务时间：10 秒
```

点击确定。

## 5. 操作

进入：

```text
操作 → 新建
```

### 程序/脚本

```text
C:\Windows\System32\wscript.exe
```

### 添加参数

```text
"D:\Tool\OmniRoute\start-omniroute.vbs"
```

### 起始于

留空。

点击确定。

## 6. 条件

进入：

```text
条件
```

取消：

```text
□ 只有在计算机使用交流电源时才启动
```

## 7. 设置

进入：

```text
设置
```

保持：

```text
☑ 允许按需运行任务
```

即可。

## 8. 保存

点击确定。

输入 Windows 用户密码（如果系统要求）。

## 9. 测试

在任务计划程序库找到：

```text
OmniRoute
```

右键：

```text
运行
```

等待几秒。

CMD 执行：

```bat
netstat -ano | findstr :20128
```

出现：

```text
LISTENING
```

说明启动成功。

## 10. 重启测试

重启电脑 → 登录 Windows → 等约 10 秒。

执行：

```bat
netstat -ano | findstr :20128
```

如果出现：

```text
TCP    0.0.0.0:20128    0.0.0.0:0    LISTENING
```

说明 **OmniRoute 已经实现开机自启**。

### 最终配置

```text
Windows 登录
    ↓
任务计划程序
    ↓
wscript.exe
    ↓
start-omniroute.vbs
    ↓
OmniRoute
    ↓
20128
```

**不要再同时启用 `omniroute autostart`，避免重复启动。**