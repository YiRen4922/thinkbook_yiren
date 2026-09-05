# 开发环境搭建
qtcreator安装
# [[C++|C++入门]]
# Windows开发Qt
## qmake示例
```cmake

QT+=core gui  # 往QT工程里面加入core gui模块
greaterThan(QT_MAJOR_VERSION，4):QT+= widgets # 假如QT库的版本大于4，就加入—个widgets的模块
TARGET = class1  # 生成APP的名字
TEMPLATE = app # 编译产物的类型，应用程序或者库

#The following define makes your compiler emit warnings if you use
#any feature of Qt which has been marked as deprecated （the exact warnings
#depend on your compiler). Please consult the documentation of the
#deprecated API in order to know how to port your code away from it.

DEFINES += QT_DEPRECATED_WARNINGS  # 定义的一个宏

#You can also make your code fail to compile if you use deprecated APIs.
#In order to do so,uncomment the following line.
#You can also select to disable deprecated APIs only up to a certain version of Qt.

#DEFINES += QT_DISABLE_DEPRECATED_BEFORE=0x060000 #disables all the APIs deprecated before Qt 6.0.0
SOURCES +=\ # 指定工程里面都有哪些cpp源文件
main.cpp\
widget.cpp
HEADERS +=\ # 指定工程里面都有哪些头文件
widget.h 
FORMS +=\ # 指定工程里面都有哪些ui文件
widget.ui 
```
## 信号与槽
控件改名：通俗易懂，见名知意
### 信号
每个控件都有信号，特定操作出发特定信号
比如按钮qpushbutton继承自，qabstractbutton，他有clicked,pressed,released,toggled
信号声明需要有signals关键字
### 槽
槽就是函数，可以把信号绑定到槽上
### 关联方法
1. 自动关联
ui界面进行自动关联：点击控件，右键，点击转到槽，选择信号，这个槽函数（on_对象名_信号名）就与信号关联了
槽函数声明需要有slots关键字
2. 手动关联
connect函数
## 仿写智能家居界面（简单示例）

### 添加图片

1. 第一步，添加文件资源，文件栏，右键，添加新文件，选qt，qt resources文件，填名字
文件栏可以看到资源文件，将图片放到工程目录下，右键资源文件，Open with，添加资源编辑器，添加前缀，可设为根目录，保存再添加文件
2. 第二步：显示图片，使用qlabel，右键，选择样式表，border-image，选择图片，显示
也可以使用按钮pushbutton显示图片
图标网站：easyicon

### 布局
垂直布局：vertical
水平布局：horizontal
栅格布局：grid
弹簧
布局不会影响代码
### 界面切换
1. 添加新界面
添加新文件>Qt>Qt设计师界面
固定大小与之前的界面相同
2. 代码触发
在槽函数中写如下代码：
```C++
// 创建新窗口
ctrl *ct=new ctrl;
// 获取当前窗口大小并设置为新窗口大小
ct->setGeometry(this->geometry());
// 显示新窗口
ct->show(）;
```

# qt三驾马车

## 串口编程
### 界面
接收方控件：plain text display
串口号，波特率，数据位，停止位，校验位选择：combo box，设置值
发送框：line edit
打开，关闭，发送，清空：pushbutton
广告框（信息说明）：groupbox
布局，栅格布局保证combox长度一样
控件改名：要求：见名知意
### 编程实现功能
1. 开启串口
qmake += serialport，保存
添加头文件qserialportinfo
foreach 遍历 可用串口名，添加到serial_name_combox
创建serialport类型
打开按钮clicked槽函数，进行串口初始化
使用qt提供的串口相关数据类型，创建串口信息变量
获取串口信息combox值，设置串口信息变量值
然后设置串口类型的相关值
打开串口（开启判重）
已经打开弹提示框（Qmessagebox）
2. 关闭串口
关闭pushbutton的clicked的槽函数，使用serialport的close函数即可
3. 接收数据
使用serialport类继承的qiodevice的readyread信号，关联槽函数，槽函数命名：见名知意
使用serialport的readall函数，appendplaintext到接受消息框plain text display控件中
4. 发送数据
获取发送串口数据转char*，使用串口的write函数发送
5. 清空
调用clear即可
### 打包，部署（windows）
源码不会随便给出共享（？）
1. 构建切换到release模式，去除debug信息，程序体积更小
2. 找到release的输出文件夹
3. 修改图标（ico格式）
   RC_ICONS=xxxx.ico
   并将图标文件放入qt项目工作目录
4. 创建新的文件夹（不要有中文），放入exe文件
   打开qt控制台（带有环境）Qt x.y.z for Desktop cmd
   qt控制台进入新文件夹中。执行`windeployqt xxxx.exe`命令
   此时可以将文件夹打包发送给其他人使用（绿色版本，无需安装）
5. 制作成安装包的形式
   


## 网络编程
### TCP
主要用到两个类QTcpServerQTcpClient
qmake+=network
### UDP

## GPIO



移植Qt环境（开发板）
移植Qt程序
Qt控制驱动
Qt开发手机APP（Android）
远程调试ARM开发板Qt程序