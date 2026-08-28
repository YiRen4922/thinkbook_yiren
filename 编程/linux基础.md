# FHS（filesystem hierarch standard）文件系统层次标准
- / 主层次结构的根&&整个文件系统的根目录
    - `/bin` 所有用户在单用户模式中必须具备的二进制命令文件，如 cat, ls, cp.（重要的二进制 (binary) 应用程序包含二进制文件，系统的所有用户使用的命令都在这个目录下）
    - `/boot` 启动 (boot loader) 配置文件，包含引导加载程序相关的文件，如 kernels, initrd.
    - `/dev` 必要的 设备(device) 文件,包含设备文件、终端设备，USB或连接到系统的任何设备，如 /dev/null.你只要通过访问这个目录下的某个文件就相当于访问某个设备. **设备分类**：`字符设备`、`块设备`（物理设备、虚拟设备）
    - `/etc` 特定主机 全系统 的配置文件(配置文件、启动脚本等)，包含所有程序所需的配置文件，也包含了用于启动/停止单个程序的启动和关闭shell脚本。一直以来,这个名字本身就有争议。在早期由`Bell labs`所撰写的UNIX实现文档中，`/etc` 被当作附加(etcetera)目录，因为历史上这个文件夹用来保存所有不属于其他地方的文件（但FHS限制/etc仅用于保存静态配置文件，不能保存二进制文件）。从早期的文档发布以来，这个文件夹的名字就被人们以不同的方式重新定义。最近的释义包括如”`Editable Text Configuration`” 或 “Extended Tool Chest”词源
        - /etc/opt　 保存在/opt/中的插件包的配置文件
        - /etc/sgml 　处理SGML的程序（如catalogs）的配置文件
        - /etc/X11 　X Window System, version 11 的配置文件
        - /etc/xml　 处理xml的程序（如catalogs）的配置文件
    - `/home` 本地用户主 (home) 目录，所有用户用home目录来存储他们的个人文件、个人设置等
    - `/lib` 系统库 (libraries) 文件（ldd？跟踪依赖库），包含支持位于/bin和/sbin下的二进制文件的库文件。即 `/bin/` 和 `/sbin/` 中必须的依赖库
    - /lib Alternate format essential libraries. Such directories are optional, but if they exist, they have some requirements.
    - `/media` 挂载(可移动介质)热插拔介质 (media)，诸如 `CD-ROMs`、`数码相机`等挂载点，用于挂载可移动设备的临时目录。 (在FHS-2.3中出现).
    - `/mnt` 临时挂载的文件系统。临时安装目录，系统管理员可以挂载文件系统：`mount -t type [-o options] device dir`
    - `/opt` 可选的、提供第三方应用程序的安装目录。
    - `/proc` 将进程和内核信息以文件形式呈现的虚拟文件系统。在Linux中，与procfs mount(进程文件系统)对应。即：特殊的动态目录，用以维护系统信息和状态，包括当前运行中进程 (processes) 信息；包含系统进程的相关信息，是一个虚拟的文件系统，包含有关正在运行的进程的信息，系统资源以文本信息形式存在。
    - `/root` root用户的用户主目录
    - `/run` 运行时变量数据:从本次启动到现在的系统信息。如当前登陆的用户和正在运行的守护进程。挂载临时文件系统，文件和目录没有存储在磁盘上，而只存储在内存中。它们表示**保存在内存（或基于磁盘的交换空间）中的数据**，它看起来像是一个已挂载的文件系统，这个可以使其更易于访问和管理
    - `/sbin` 必备的系统可执行文件，如fsck, init, route.即：重要的系统二进制 (system binaries) 文件 也是包含的二进制可执行文件。在这个目录下的linux命令**通常都是由系统管理员使用**的，对系统进行维护。即 **/sbin**一般存放`root用户`的管理类程序；**/bin**`一般用户`都可以使用的命令
    - `/srv` 本系统提供的特定站点的数据，存放着一些软件服务启动后所需要的程序。如web服务器提供的数据和脚本，FTP服务器提供的数据，VCS的仓库
    - `/sys` 包含连接到本台计算机的设备信息.即 系统 (system) 文件，其实跟/proc非常的相似，也是一个虚拟的文件系统主要也是记录与内核相关的信息
    - `/tmp` 临时文件(和/var/tmp相同). 通常在重启后清空，并且受到严格的大小限制。 即 临时(temporary)文件包含系统和用户创建的临时文件。当系统重启时，这个目录下的文件将都被删除
    - `/usr` UNIX SOFTWARE RESOURCE包含绝大部分用户都能访问的应用程序和文件，包含二进制文件，库文件、文档和二级程序的源代码。即: 只读用户数据的次要层次，包含大部分（多）用户功能和应用。
        - /usr/bin 　　　所有用户的非必要的二进制可执行文件(在单用户模式中不需要)
        - /usr/include 　Standard include files.
        - /usr/lib 　　　　/usr/bin/ 和 /usr/sbin/ 中的二进制文件的依赖库
        - /usr/lib Alternate format libraries (optional).
        - /usr/local 　　　仅针对当前主机的 本地数据的第三个层次。一般包含其他的子目录，如 bin/, lib/, share/
        - /usr/sbin 　　　非必须的系统二进制文件，如多种网络服务的守护进程
        - /usr/share 　　结构独立（共享）的数据
        - /usr/src 　　　源代码，如 内核的源代码和它的头文件
        - /usr/X11R6 　　X Window System, Version 11, Release 6 (up to FHS-2.3, optional).
    - `/var` Variable files:各种在系统运行中，内容会不停改变的文件。经常变化的(variable)文件，在这个目录下可以找到内容可能增长的文件。如：日志文件、数据库、spool files，和临时的电子邮件文件。
        - /var/cache 应用缓存数据。这类文件由于耗时的I/O或计算而被生成在本地。应用必须能够重新生成或转储这些文件，以保证这些数据被删除时不会造成数据丢失。（意思就是这些东西删了不会造成不良后果）
        - /var/lib 　　状态信息。程序运行时会改变的持久化数据，如 数据库，packaging system metadata, etc.
        - /var/lock 　Lock files. 追踪当前正在使用的资源的文件.
        - /var/log 　Log files. 各种日志.
        - /var/mail 　Mailbox files. 在某些发行版中，这些文件被放在已经不推荐使用的/var/spool/mail 目录中.
        - /var/opt 　来自保存在/opt 中的插件包的可变数据。
        - /var/run 　Run-time variable data. 这个目录包含描述系统的自启动以来的系统信息数据。在 FHS 3.0中， /var/run 被 /run 替代。系统不应该在使用/var/run 或者提供/var/run 到 /run 的符号连接，防止出现兼容性倒退
        - /var/spool Spool for tasks waiting to be processed, e.g., print queues and outgoing mail queue.
            - /var/spool/mail 不建议使用的用户邮箱位置，见/var/mail
        - /var/tmp 重启时会被保存的临时数据

# 文件
1. **ls**

- `ls` 列出当前目录文件
- `ls -l` 展示详细属性
- `ls -a` 显示隐藏文件

2. **pwd**

- `pwd` 打印当前工作目录路径

3. **cd**

- `cd /etc` 切换到指定目录
- `cd ..` 返回上一级目录
- `cd ~` 切换到用户家目录

4. **mkdir**

- `mkdir test` 创建目录
- `mkdir -p a/b/c` 递归创建多级目录

5. **rmdir**

- `rmdir test` 删除空目录

6. **rm**

- `rm file.txt` 删除文件
- `rm -r dir` 递归删除目录
- `rm -rf dir` 强制递归删除

7. **cp**

- `cp src.txt dst.txt` 复制文件
- `cp file.txt /tmp/` 复制到目标目录
- `cp -r dir1 dir2` 复制目录

8. **mv**

- `mv old.txt new.txt` 重命名
- `mv file.txt /tmp/` 移动文件
- `mv dir1 /home/` 移动目录

9. **cat**

- `cat file.txt` 输出文件全部内容

10. **more**

- `more file.txt` 分页浏览文件

11. **less**

- `less file.txt` 交互式分页查看

12. **head**

- `head file.txt` 查看文件前 10 行
- `head -n 20 file.txt` 查看前 20 行

13. **tail**

- `tail file.txt` 查看文件末尾 10 行
- `tail -f log.txt` 实时追踪文件新增内容

14. **touch**

- `touch test.txt` 创建空文件 / 更新时间戳

15. **find**

- `find . -name "*.txt"` 当前目录查找 txt 文件
- `find /home -type f` 查找普通文件

16. **locate**

- `locate nginx.conf` 数据库快速检索文件

17. **ln**

- `ln file hardlink` 创建硬链接
- `ln -s /opt/src softlink` 创建软链接

18. **du**

- `du -sh *` 查看各文件目录总大小

19. **stat**

- `stat file.txt` 查看文件 inode、权限、时间等元数据

20. **grep**

- `grep "key" file.txt` 文件内搜索关键字
- `grep -i "key" file.txt` 忽略大小写搜索

21. **tar**

- `tar -zcvf test.tar.gz dir/` gzip 打包压缩
- `tar -zxvf test.tar.gz` 解压 gz 压缩包

22. **zip**

- `zip -r out.zip dir/` 压缩目录为 zip

23. **unzip**

- `unzip out.zip` 解压 zip 压缩包

# 进程

下面按你给出的格式整理，**重点限定在 Linux「进程管理与控制」**，不把文件、网络、磁盘等无关命令混进来。

我会按照「查看 → 查找 → 控制 → 前后台 → 优先级 → 资源限制 → 进程树 → 守护进程/服务」来组织。

---

# 一、进程查看

### 1. ps

`ps` 查看当前进程状态

```bash
ps
```

查看当前终端下的进程

```bash
ps -e
```

查看系统中所有进程

```bash
ps -ef
```

以完整格式显示所有进程

```bash
ps aux
```

以 BSD 格式显示所有进程，包括 CPU、内存占用

```bash
ps -u 用户名
```

查看指定用户的进程

```bash
ps -p PID
```

查看指定 PID 的进程

```bash
ps -f -p PID
```

查看指定 PID 的详细信息

```bash
ps --forest
```

以树状结构显示进程关系

---

### 2. pstree

`pstree` 以树状结构显示进程之间的父子关系

```bash
pstree
```

显示进程树

```bash
pstree -p
```

显示 PID

```bash
pstree -a
```

显示完整命令行参数

```bash
pstree -p 用户名
```

查看指定用户的进程树

---

### 3. top

`top` 实时查看系统进程

```bash
top
```

实时查看进程 CPU、内存等信息

```bash
top -p PID
```

只查看指定进程

```bash
top -u 用户名
```

只查看指定用户的进程

常用交互操作：

```text
P    按 CPU 使用率排序
M    按内存使用率排序
N    按 PID 排序
T    按运行时间排序
k    杀死指定进程
r    修改进程优先级
1    显示每个 CPU
q    退出
```

---

### 4. htop

`htop` 是 `top` 的增强版，交互式查看进程

```bash
htop
```

查看所有进程

```bash
htop -u 用户名
```

查看指定用户进程

```bash
htop -p PID
```

查看指定进程

---

### 5. pidof

`pidof` 根据程序名称查找 PID

```bash
pidof nginx
```

查找 nginx 的 PID

```bash
pidof sshd
```

查找 sshd 的 PID

---

### 6. pgrep

`pgrep` 根据名称、用户等条件查找进程

```bash
pgrep nginx
```

查找 nginx 的 PID

```bash
pgrep -a nginx
```

显示 PID 和完整命令

```bash
pgrep -u username
```

查找指定用户的进程

```bash
pgrep -P PID
```

查找指定父进程的子进程

```bash
pgrep -f "python app.py"
```

根据完整命令行匹配进程

---

# 二、进程详细信息

### 7. cat /proc/PID/status

`/proc/PID/status` 查看进程详细状态

```bash
cat /proc/1234/status
```

查看 PID 1234 的进程状态

可以看到：

```text
Name
State
Pid
PPid
Uid
Gid
Threads
VmSize
VmRSS
```

---

### 8. cat /proc/PID/cmdline

`/proc/PID/cmdline` 查看进程启动命令

```bash
cat /proc/1234/cmdline
```

---

### 9. cat /proc/PID/exe

`/proc/PID/exe` 查看进程对应的可执行文件

```bash
readlink /proc/1234/exe
```

---

### 10. cat /proc/PID/cwd

`/proc/PID/cwd` 查看进程当前工作目录

```bash
readlink /proc/1234/cwd
```

---

### 11. cat /proc/PID/fd

`/proc/PID/fd` 查看进程打开的文件描述符

```bash
ls -l /proc/1234/fd
```

---

### 12. cat /proc/PID/environ

`/proc/PID/environ` 查看进程环境变量

```bash
cat /proc/1234/environ
```

通常配合：

```bash
tr '\0' '\n' < /proc/1234/environ
```

将环境变量逐行显示。

---

# 三、进程终止与信号控制

Linux 中**进程控制的核心就是信号（signal）**。

---

### 13. kill

`kill` 向进程发送信号

```bash
kill PID
```

默认发送 `SIGTERM`，请求进程正常退出

```bash
kill -TERM PID
```

发送 `SIGTERM`

```bash
kill -KILL PID
```

强制终止进程

也可以：

```bash
kill -9 PID
```

`-9` 就是 `SIGKILL`

```bash
kill -STOP PID
```

暂停进程

```bash
kill -CONT PID
```

继续运行被暂停的进程

```bash
kill -HUP PID
```

发送 `SIGHUP`

查看所有信号：

```bash
kill -l
```

---

### 14. pkill

`pkill` 根据进程名称等条件发送信号

```bash
pkill nginx
```

结束 nginx 进程

```bash
pkill -9 nginx
```

强制结束 nginx

```bash
pkill -u username
```

结束指定用户的所有进程

```bash
pkill -P PID
```

结束指定父进程的子进程

---

### 15. killall

`killall` 根据程序名称杀死进程

```bash
killall nginx
```

结束所有名为 nginx 的进程

```bash
killall -9 nginx
```

强制结束 nginx

```bash
killall -u username
```

结束指定用户的进程

> `kill` 主要针对 PID，`pkill/killall` 主要针对进程名称或匹配条件。

---

### 16. xkill

`xkill` 用于图形界面中关闭窗口对应的进程

```bash
xkill
```

然后点击目标窗口即可结束对应程序。

主要用于 X11 环境。

---

# 四、Shell 作业控制

这里要特别区分：

**进程（process）** 和 **Shell Job（作业）** 不是完全相同的概念。

---

### 17. jobs

`jobs` 查看当前 Shell 的后台作业

```bash
jobs
```

显示后台任务

```bash
jobs -l
```

同时显示 PID

例如：

```text
[1]+  1234 Running    ./test &
```

---

### 18. &

`&` 将程序放到后台运行

```bash
./test &
```

让程序后台运行。

---

### 19. Ctrl + Z

`Ctrl + Z` 暂停当前前台进程

例如：

```bash
./test
```

运行过程中：

```text
Ctrl + Z
```

进程会进入 `Stopped` 状态。

---

### 20. bg

`bg` 让暂停的任务在后台继续运行

```bash
bg
```

继续运行最近暂停的任务

```bash
bg %1
```

让 Job 1 在后台运行

---

### 21. fg

`fg` 将后台任务恢复到前台

```bash
fg
```

恢复最近的后台任务

```bash
fg %1
```

恢复 Job 1

---

### 22. disown

`disown` 将任务从当前 Shell 的作业列表中移除

```bash
disown
```

移除当前任务

```bash
disown %1
```

移除 Job 1

常用于：

```bash
./program &
disown
```

这样退出终端后，程序通常不会因为 Shell 的作业控制而被一起结束。

---

### 23. nohup

`nohup` 让程序忽略 `SIGHUP`，常用于退出终端后继续运行

```bash
nohup ./program &
```

默认输出通常进入：

```text
nohup.out
```

例如：

```bash
nohup ./server > server.log 2>&1 &
```

---

### 24. wait

`wait` 等待子进程结束

```bash
wait
```

等待当前 Shell 的所有后台任务

```bash
wait PID
```

等待指定 PID

```bash
wait %1
```

等待 Job 1

---

# 五、进程优先级

Linux 使用 **nice 值**影响普通进程调度优先级。

一般范围：

```text
-20   最高优先级
  0   默认
+19   最低优先级
```

---

### 25. nice

`nice` 以指定 nice 值启动程序

```bash
nice ./program
```

默认增加 nice 值

```bash
nice -n 10 ./program
```

以 nice = 10 的优先级启动

```bash
sudo nice -n -10 ./program
```

以更高优先级运行。

---

### 26. renice

`renice` 修改已经运行进程的 nice 值

```bash
renice 10 -p PID
```

将 PID 对应进程设置为 nice 10

```bash
sudo renice -10 -p PID
```

提高进程优先级

```bash
renice 10 -u username
```

修改指定用户进程的 nice 值

---

### 27. chrt

`chrt` 查看或修改进程的调度策略和实时优先级

```bash
chrt -p PID
```

查看进程调度策略

```bash
sudo chrt -r -p 50 PID
```

设置为 `SCHED_RR`，实时优先级 50

```bash
sudo chrt -f -p 50 PID
```

设置为 `SCHED_FIFO`

```bash
chrt -p PID
```

查看当前进程调度信息。

这个命令在**实时系统、嵌入式 Linux**中特别重要。

---

# 六、进程 CPU 绑定

### 28. taskset

`taskset` 设置进程可以运行在哪些 CPU 上

```bash
taskset -p PID
```

查看进程 CPU affinity

```bash
taskset -cp PID
```

查看进程允许使用的 CPU

```bash
taskset -cp 0 PID
```

让进程只运行在 CPU 0

```bash
taskset -cp 0,1 PID
```

让进程运行在 CPU 0、1

启动时指定：

```bash
taskset -c 0 ./program
```

---

# 七、进程资源限制

### 29. ulimit

`ulimit` 查看或设置 Shell/进程资源限制

```bash
ulimit -a
```

查看所有限制

```bash
ulimit -n
```

查看最大文件描述符数量

```bash
ulimit -u
```

查看最大进程/线程数量限制

```bash
ulimit -c
```

查看 Core Dump 大小限制

```bash
ulimit -n 65535
```

设置最大文件描述符数量

```bash
ulimit -c unlimited
```

允许生成无限大小的 Core Dump。

---

### 30. prlimit

`prlimit` 查看或修改指定进程的资源限制

```bash
prlimit --pid PID
```

查看进程资源限制

```bash
prlimit --pid PID --nofile=65535
```

修改文件描述符限制

```bash
prlimit --pid PID --core=unlimited
```

修改 Core Dump 限制

---

# 八、进程资源监控

### 31. pmap

`pmap` 查看进程内存映射

```bash
pmap PID
```

查看进程内存布局

```bash
pmap -x PID
```

显示详细内存统计

对于分析：

```text
虚拟内存
共享库
堆
栈
mmap
```

很有用。

---

### 32. free

`free` 查看系统内存

```bash
free
```

查看内存

```bash
free -h
```

以易读格式显示

```bash
free -m
```

以 MB 显示。

它不是专门查看单个进程，而是查看**系统整体内存状态**。

---

### 33. vmstat

`vmstat` 查看系统整体资源和进程状态

```bash
vmstat
```

查看一次统计

```bash
vmstat 1
```

每秒刷新一次

```bash
vmstat 1 10
```

每秒刷新一次，共 10 次

可以观察：

```text
进程
内存
Swap
IO
CPU
```

---

### 34. pidstat

`pidstat` 专门用于进程级性能统计

```bash
pidstat
```

查看进程统计

```bash
pidstat 1
```

每秒统计一次

```bash
pidstat -p PID 1
```

查看指定进程

```bash
pidstat -u 1
```

查看 CPU 使用情况

```bash
pidstat -r 1
```

查看内存情况

```bash
pidstat -d 1
```

查看 IO 情况

---

# 九、进程打开的资源

### 35. lsof

`lsof` 查看进程打开的文件和资源

```bash
lsof -p PID
```

查看指定进程打开的所有文件

```bash
lsof -i
```

查看网络连接

```bash
lsof -i :8080
```

查看谁占用了 8080 端口

```bash
lsof /path/file
```

查看谁打开了指定文件。

Linux 中一个很重要的思想是：

> **进程通过文件描述符管理文件、管道、Socket、设备等资源。**

---

# 十、进程树与父子关系

### 36. ps --ppid

`ps --ppid` 根据父 PID 查找子进程

```bash
ps --ppid PID
```

查看指定父进程创建的子进程

例如：

```bash
ps --ppid 1
```

查看 PID 1 创建/管理的子进程。

---

### 37. pstree -p

`pstree -p` 查看完整父子关系

```bash
pstree -p
```

例如：

```text
systemd(1)
 ├─sshd(1000)
 │  └─bash(1001)
 │     └─test(1002)
```

这对于理解 Linux 进程关系非常重要。

---

# 十一、守护进程与服务控制

如果把「进程管理」扩大到 Linux 系统服务，那么还需要下面这些。

### 38. systemctl

`systemctl` 管理 systemd 服务

```bash
systemctl status nginx
```

查看服务状态

```bash
systemctl start nginx
```

启动服务

```bash
systemctl stop nginx
```

停止服务

```bash
systemctl restart nginx
```

重启服务

```bash
systemctl reload nginx
```

重新加载配置

```bash
systemctl enable nginx
```

设置开机启动

```bash
systemctl disable nginx
```

取消开机启动

```bash
systemctl is-active nginx
```

查看服务是否运行

```bash
systemctl list-units --type=service
```

查看当前服务。

---

### 39. service

`service` 是传统 SysV 风格的服务管理接口，在很多现代 Linux 中实际上会转到 systemd

```bash
service nginx status
```

查看服务

```bash
service nginx start
```

启动服务

```bash
service nginx stop
```

停止服务

```bash
service nginx restart
```

重启服务。

现代 systemd 系统优先使用：

```bash
systemctl
```

---

# 十二、终端/会话相关

### 40. loginctl

`loginctl` 管理 systemd 登录会话

```bash
loginctl
```

查看登录会话

```bash
loginctl list-sessions
```

查看所有会话

```bash
loginctl session-status
```

查看当前会话状态

```bash
loginctl terminate-session SESSION
```

终止指定会话。

---

### 41. screen

`screen` 创建终端会话，使程序可以脱离当前终端继续运行

```bash
screen
```

创建新的 screen

```bash
screen -S name
```

创建指定名称的会话

```bash
screen -ls
```

查看会话

```bash
screen -r name
```

重新连接会话

---

### 42. tmux

`tmux` 是现代 Linux 中非常常用的终端复用工具

```bash
tmux
```

创建会话

```bash
tmux new -s name
```

创建指定名称的会话

```bash
tmux ls
```

查看会话

```bash
tmux attach -t name
```

连接会话

```bash
tmux kill-session -t name
```

结束会话。

---

# 十三、Core Dump / 崩溃进程

### 43. coredumpctl

`coredumpctl` 查看 systemd 保存的进程崩溃信息

```bash
coredumpctl
```

查看崩溃记录

```bash
coredumpctl list
```

查看崩溃进程列表

```bash
coredumpctl info PID
```

查看指定进程崩溃信息

```bash
coredumpctl debug PID
```

使用调试器分析 Core Dump。

---

# 十四、几个容易混淆的命令

这几个建议重点记忆：

|命令|核心作用|
|---|---|
|`ps`|**查看进程**|
|`top`|**实时查看进程**|
|`htop`|**交互式实时查看进程**|
|`pstree`|**查看父子进程关系**|
|`pgrep`|**按条件找 PID**|
|`pidof`|**按程序名找 PID**|
|`kill`|**按 PID 发信号**|
|`pkill`|**按条件发信号**|
|`killall`|**按程序名发信号**|
|`jobs`|**查看 Shell 作业**|
|`bg`|**后台继续运行**|
|`fg`|**恢复到前台**|
|`nohup`|**忽略 SIGHUP，脱离终端运行**|
|`disown`|**从 Shell 作业控制中移除**|
|`nice`|**启动时设置优先级**|
|`renice`|**修改运行中进程优先级**|
|`chrt`|**修改实时调度策略/优先级**|
|`taskset`|**绑定 CPU**|
|`ulimit`|**设置 Shell 资源限制**|
|`prlimit`|**设置进程资源限制**|
|`pmap`|**查看进程内存映射**|
|`lsof`|**查看进程打开的资源**|
|`pidstat`|**统计进程性能**|
|`systemctl`|**管理系统服务**|

---

# 十五、最重要的一套：Linux 进程控制主线

如果你是为了**学习 Linux 系统编程 / 嵌入式 Linux / 408**，实际上不需要把上面几十个命令全部同等记忆。

建议把整个进程管理理解成下面这条线：

```text
                 Linux Process
                       │
        ┌──────────────┼──────────────┐
        ↓              ↓              ↓
      查看             查找            控制
        │              │              │
       ps             pgrep           kill
       top            pidof           pkill
       htop                            killall
       pstree
        │
        ↓
     进程关系
        │
     PPID / PID
        │
        ↓
   fork() → exec() → wait()
        │
        ↓
     父进程/子进程
```

再往下：

```text
进程控制
   │
   ├── 信号
   │    ├── SIGTERM
   │    ├── SIGKILL
   │    ├── SIGSTOP
   │    ├── SIGCONT
   │    └── SIGHUP
   │
   ├── 作业控制
   │    ├── jobs
   │    ├── bg
   │    ├── fg
   │    └── Ctrl+Z
   │
   ├── 调度
   │    ├── nice
   │    ├── renice
   │    └── chrt
   │
   ├── CPU
   │    └── taskset
   │
   └── 资源
        ├── ulimit
        └── prlimit
```

## 如果你是在整理 Linux 命令笔记

那么我建议最终把「进程管理」压缩成下面这套**核心命令表**：

```text
1. ps
ps                  查看当前进程
ps -ef              查看所有进程
ps aux              查看所有进程及资源占用
ps -p PID           查看指定进程
ps --forest         树状显示进程

2. top
top                 实时查看进程
top -p PID          查看指定进程

3. htop
htop                交互式实时查看进程

4. pstree
pstree              查看进程树
pstree -p           显示 PID

5. pgrep
pgrep name          按名称查找 PID
pgrep -a name       显示 PID 和命令

6. pidof
pidof name          根据程序名查找 PID

7. kill
kill PID            发送 SIGTERM
kill -9 PID         发送 SIGKILL
kill -STOP PID      暂停进程
kill -CONT PID      继续进程
kill -l             查看信号

8. pkill
pkill name          按名称发送信号
pkill -9 name       强制结束

9. killall
killall name        根据程序名结束进程
killall -9 name     强制结束

10. jobs
jobs                查看当前 Shell 后台作业
jobs -l             显示作业 PID

11. bg
bg                  后台继续运行
bg %1               后台运行 Job 1

12. fg
fg                  恢复到前台
fg %1               恢复 Job 1

13. nohup
nohup ./app &       脱离终端运行

14. disown
disown              从 Shell 作业列表移除

15. wait
wait                等待后台进程结束
wait PID             等待指定进程

16. nice
nice ./app           低优先级启动
nice -n 10 ./app     指定 nice 值启动

17. renice
renice 10 -p PID     修改进程 nice 值

18. chrt
chrt -p PID          查看调度策略
chrt -r -p 50 PID    设置实时调度策略

19. taskset
taskset -cp PID      查看 CPU affinity
taskset -cp 0 PID    绑定 CPU 0

20. ulimit
ulimit -a            查看资源限制
ulimit -n            查看文件描述符限制
ulimit -c unlimited   开启无限 Core Dump

21. prlimit
prlimit --pid PID    查看进程资源限制

22. pmap
pmap PID             查看进程内存映射
pmap -x PID          查看详细内存

23. pidstat
pidstat 1            监控进程
pidstat -u 1         CPU
pidstat -r 1         内存
pidstat -d 1         IO

24. lsof
lsof -p PID          查看进程打开的文件
lsof -i :8080        查看端口占用

25. systemctl
systemctl status xxx     查看服务
systemctl start xxx      启动服务
systemctl stop xxx       停止服务
systemctl restart xxx    重启服务
systemctl enable xxx     开机启动
systemctl disable xxx    取消开机启动
```

**其中最值得你进一步理解的不是命令本身，而是 `PID → PPID → fork → exec → wait → signal → scheduler → /proc` 这一整套关系。** 这才是 Linux 进程管理的核心。