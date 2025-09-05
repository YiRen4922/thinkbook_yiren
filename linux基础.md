FHS（filesystem hierarch standard）文件系统层次标准
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


链接文件
硬链接：
ln
软链接：
ln -s