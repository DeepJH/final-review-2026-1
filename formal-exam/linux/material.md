**《Linux操作系统》复习资料**

**一、Linux基础知识**

**1.1 起源与核心概念**

| **知识点** | **内容** |
| --- | --- |
| Linux创始人 | **Linus Torvalds** |
| 引导程序 | **Bootloader**（引导操作系统内核的程序） |
| 查看/编辑内核路由表 | **route** 命令 |
| root用户UID | **0** |

**二、用户与权限管理**

**2.1 用户管理命令**

| **操作** | **命令** |
| --- | --- |
| 创建用户 | useradd / adduser |
| 删除用户（同时删主目录） | userdel -r |
| 修改密码 | passwd |
| 查看用户信息文件 | /etc/passwd（不含密码）、/etc/shadow（含加密密码） |

**2.2 权限管理**

| **知识点** | **内容** |
| --- | --- |
| 修改权限 | chmod |
| 修改属主 | chown |
| 修改属组 | chgrp |
| 查看umask | umask |
| 权限数字对应 | r=4, w=2, x=1 |

**2.3 特殊权限位**

| **权限** | **数字** | **字母法** | **作用** |
| --- | --- | --- | --- |
| **setgid** | 2 | g+s | 目录下新建文件继承目录属组 |
| **setuid** | 4 | u+s | 以文件属主身份执行 |
| **sticky bit** | 1 | o+t | 仅属主可删除文件（/tmp） |

**2.4 经典计算**

**rwxr-xr-x = 755**（属主7，属组5，其他5） **rw-r--r-- = 644**（属主6，属组4，其他4）

**三、Shell编程**

**3.1 预定义变量**

| **变量** | **含义** |
| --- | --- |
| $? | 上一个命令的退出状态（0=成功，非0=失败） |
| $! | 后台最后一个进程的PID |
| $0 | 当前执行的进程名 |
| $1 | 脚本第一个参数 |
| $# | 参数个数 |
| \$* / \$@ | 所有参数 |

**3.2 有条件的变量替换**

| **语法** | **含义** |
| --- | --- |
| ${param:-word} | param未设置或为空，则用word |
| ${param:=word} | param未设置或为空，则用word**并赋值** |
| ${param:?word} | param未设置或为空，则报错word |
| ${param:+word} | param已设置且非空，则用word |

**3.3 逻辑运算符**

| **运算符** | **含义** | **示例** |
| --- | --- | --- |
| && | 前成功才执行后 | cmd1 && cmd2 |
| \|\| | 前失败才执行后 | cmd1 \|\| cmd2 |
| ; | 顺序执行 | cmd1; cmd2 |

**3.4 文件测试**

| **测试** | **含义** |
| --- | --- |
| -f | 普通文件存在 |
| -d | 目录存在 |
| -e | 文件/目录存在 |
| -r | 可读 |
| -w | 可写 |
| -x | 可执行 |

**四、软件包管理**

**4.1 RPM vs YUM/DNF 对比**

| **特性** | **RPM** | **YUM/DNF** |
| --- | --- | --- |
| 级别 | 低级包管理器 | 高级包管理器 |
| 依赖处理 | ❌ 无法自动解决 | ✅ 自动解决依赖 |
| 网络仓库 | ❌ 不支持 | ✅ 支持 |
| 常用命令 | rpm -ivh（安装）、rpm -e（卸载）、rpm -ql（查文件） | yum install、yum remove |
| RHEL 8默认 | — | **DNF**（YUM的继任者） |

**4.2 仓库结构（RHEL 8）**

| **仓库** | **用途** |
| --- | --- |
| **BaseOS** | 操作系统底层核心软件，生命周期与发行版一致 |
| **AppStream** | 应用程序流 |
| EPEL | 额外企业级Linux包 |
| RPM Fusion | 第三方包 |

**4.3 本地YUM仓库搭建（离线场景）**

mount /dev/cdrom /mnt
yum install createrepo -y
createrepo /mnt
\# 创建 /etc/yum.repos.d/local.repo:
\# [LocalRepo]
\# baseurl=file:///mnt
\# enabled=1
\# gpgcheck=0
yum clean all && yum makecache
yum install httpd -y

**五、网络管理**

**5.1 RHEL 8网络管理核心**

| **工具** | **用途** |
| --- | --- |
| **NetworkManager** | 管理网络连接、检测网络、自动连接 |
| **nmcli** | CLI方式管理NetworkManager（**最佳实践**） |
| **firewalld** | 防火墙管理（RHEL 7/8默认） |
| iproute2 | 替代net-tools的新工具集（ip addr、ip route、ss） |

**5.2 nmcli配置静态IP（RHEL 8）**

nmcli connection add type ethernet con-name static-eth0 ifname eth0
nmcli connection modify static-eth0 ipv4.addresses 192.168.1.100/24
nmcli connection modify static-eth0 ipv4.gateway 1992.168.1.1
nmcli connection modify static-eth0 ipv4.dns "8.8.8.8 114.114.114.114"
nmcli connection modify static-eth0 ipv4.method manual
nmcli connection up static-eth0

**5.3 firewalld常用命令**

| **操作** | **命令** |
| --- | --- |
| 放行服务（永久） | firewall-cmd --permanent --add-service=http |
| 重载配置 | firewall-cmd --reload |
| 查看状态 | systemctl status firewalld |

**5.4 TCP Wrappers**

| **知识点** | **内容** |
| --- | --- |
| 控制文件 | /etc/hosts.allow 和 /etc/hosts.deny |
| 检查顺序 | **先** hosts.allow，**再** hosts.deny |
| 示例 | sshd : 192.168.10.0/24（允许该网段SSH） |
| 工作层 | 传输层 |

**5.5 DHCP过程**

**DISCOVER → OFFER → REQUEST → ACK**

**六、进程与系统监控**

| **命令** | **用途** | **特点** |
| --- | --- | --- |
| ps aux / ps -ef | 查看进程快照 | 静态信息 |
| top / htop | 实时监控CPU、内存 | 动态刷新 |
| free | 查看内存使用 | 总内存、已用、空闲、缓存 |
| lsof | 查看打开的文件/端口 | lsof -i :80 查80端口 |
| pstree | 树状显示进程 | — |
| uptime / w | 负载与登录用户 | — |
| kill / pkill / killall | 终止进程 | kill PID / pkill 进程名 |
| crontab | 定时任务 | 每天2:30 → 30 2 \* \* \* |

**七、Vim编辑器**

| **操作** | **按键** |
| --- | --- |
| 命令模式→输入模式 | **i** |
| 删除当前行并进入输入模式 | **cc** |
| 保存退出 | :wq |
| 命令模式进入 | **Esc** |

**八、文件系统与磁盘**

| **命令** | **用途** |
| --- | --- |
| df | 查看磁盘空间占用 |
| du | 统计目录/文件占用空间 |
| lsblk | 查看块设备 |
| fdisk | 磁盘分区 |
| 常见文件系统 | ext3、ext4、xfs |

**九、Web服务（Apache）**

| **知识点** | **内容** |
| --- | --- |
| RHEL 8默认Web服务器 | **Apache HTTP Server (httpd)** |
| 主配置文件 | /etc/httpd/conf/httpd.conf |
| 关键指令 | ServerRoot（根目录）、Listen（监听端口）、DocumentRoot（文档根目录）、DirectoryIndex（默认索引） |
| 启动与开机自启 | systemctl start httpd + systemctl enable httpd |

**十、SELinux**

| **模式** | **含义** |
| --- | --- |
| **Enforcing** | 强制执行安全策略 |
| **Permissive** | 记录违规但不阻止 |
| **Disabled** | 完全禁用 |
| 临时切换Permissive | setenforce 0 |
| 查看安全上下文 | ls -Zd 目录路径 |

**十一、常考易错点汇总**

| **易错点** | **正确答案** |
| --- | --- |
| Linux只能在Intel x86上运行？ | ❌ 错，支持x86、x64、ARM、SPARC等 |
| RPM根据扩展名.rpm判断格式？ | ❌ 错，根据文件头信息 |
| ifconfig配置永久生效？ | ❌ 错，RHEL 8中不永久 |
| 定义变量时加$？ | ❌ 错，定义时不加$，引用时才加 |
| firewalld运行时配置永久生效？ | ❌ 错，需加--permanent |
| rmdir删非空目录？ | ❌ 错，只能删空目录 |
| rpm -e卸载时强制忽略依赖？ | ❌ 错，默认检查依赖 |
| TCP Wrappers先检查deny？ | ❌ 错，先allow再deny |
| useradd默认创建主目录？ | ❌ 错，需加-m |
| DHCP OFFER先发？ | ❌ 错，客户端先DISCOVER |
| iptables在RHEL 8完全淘汰？ | ❌ 错，firewalld后端可用iptables/nftables |

《Linux操作系统》练习一

一、 单项选择题（每题1分，共20分）

1. Linux操作系统的起源与哪位开发者紧密相关？（ ）
   A. Bill Gates
   B. Steve Jobs
   C. Linus Torvalds
   D. Richard Stallman
   **答案：C**
   **讲解：** Linux内核由Linus Torvalds于1991年创建。Bill Gates（微软）、Steve Jobs（苹果）、Richard Stallman（GNU项目）均与Linux内核的起源无直接关系。

2. 在Linux中，root用户的UID是？（ ）
   A. 1
   B. 0
   C. 1000
   D. 999
   **答案：B**
   **讲解：** root是Linux超级管理员，其UID固定为0。UID 1通常分配给bin用户，UID 1000+常用于普通用户。

3. 在RHEL 8中，默认的包管理器是？（ ）
   A. YUM
   B. DNF
   C. dpkg
   D. apt
   **答案：B**
   **讲解：** RHEL 8默认使用DNF作为包管理器，它是YUM的继任者，提供更快的依赖解析和更好的API支持。虽然`yum`命令仍可用（通过符号链接指向DNF），但底层默认是DNF。dpkg和apt是Debian/Ubuntu系列的包管理器。

4. 在Shell中，用于获取上一个命令执行后返回的状态（退出代码）的预定义变量是？（ ）
   A. $!
   B. $?
   C. $0
   D. $_
   **答案：B**
   **讲解：** $?保存上一个命令的退出状态码，0代表成功，非0代表失败。$!是后台最后一个进程的PID，$0是当前执行的进程名。

5. 在RHEL 8中，用于管理网络连接、检测网络、自动连接网络的程序是？（ ）
   A. network
   B. NetworkManager
   C. net-tools
   D. iproute2
   **答案：B**
   **讲解：** NetworkManager是RHEL 8/9中默认的网络管理服务。net-tools（如ifconfig）和iproute2（如ip命令）是底层工具集，而network是RHEL 6及之前版本使用的传统服务。

6. 使用userdel命令删除用户时，若要同时删除用户的主目录，应使用哪个参数？（ ）
   A. -r
   B. -d
   C. -m
   D. -f
   **答案：A**
   **讲解：** userdel -r中的-r表示递归删除用户的主目录和邮件池。不加-r仅删除用户账户，主目录保留。

7. 在Linux中，用于检查文件系统的磁盘空间占用情况的命令是？（ ）
   A. df
   B. du
   C. lsblk
   D. fdisk
   **答案：A**
   **讲解：** df（Disk Free）显示文件系统整体的磁盘空间使用情况。du（Disk Usage）统计单个目录或文件占用的空间。lsblk查看块设备信息，fdisk用于分区管理。

8. 在RHEL 8中，默认的Web服务器软件是？（ ）
   A. Nginx
   B. Apache HTTP Server
   C. Tomcat
   D. Lighttpd
   **答案：B**
   **讲解：** RHEL 8/9默认使用Apache HTTP Server（httpd）作为Web服务器。Nginx需额外安装，Tomcat是Java Servlet容器，Lighttpd是轻量级服务器。

9. 在Vim编辑器中，从命令模式切换到输入模式，应按哪个键？（ ）
   A. Esc
   B. i
   C. :
   D. v
   **答案：B**
   **讲解：** Vim默认处于命令模式。按`i`键切换到输入模式（insert），`Esc`是从输入模式返回命令模式，`:`进入末行模式，`v`进入可视模式。

10. 使用RPM命令安装软件包时，所用的选项是？（ ）
    A. -i
    B. -e
    C. -U
    D. -q
    **答案：A**
    **讲解：** RPM常用选项：`rpm -ivh`安装（i=install），`rpm -e`卸载（e=erase），`rpm -Uvh`升级（U=upgrade），`rpm -q`查询（q=query）。

11. 在Linux中，用于引导操作系统内核的程序通常被称为？（ ）
    A. Kernel Loader
    B. Bootloader
    C. System Initializer
    D. Startup Manager
    **答案：B**
    **讲解：** Bootloader是系统启动时第一个运行的程序，负责加载操作系统内核到内存并启动系统。常见的Bootloader有GRUB（Linux）、LILO等。

12. 在Linux中，用于查看或编辑内核路由表的命令是？（ ）
    A. ifconfig
    B. route
    C. dhclient
    D. traceroute
    **答案：B**
    **讲解：** `route`命令用于查看和操作内核IP路由表。ifconfig配置网络接口，dhclient获取DHCP地址，traceroute追踪数据包路径。

13. 在Shell中，用于显示当前执行的进程名的预定义变量是？（ ）
    A. $!
    B. $?
    C. $0
    D. $$
    **答案：C**
    **讲解：** $0代表当前脚本或进程的名称。$!是最后一个后台进程的PID，$$是当前进程的PID。

14. DHCP协议的主要目的是？（ ）
    A. 动态分配IP地址
    B. 解析域名
    C. 路由数据包
    D. 提供Web服务
    **答案：A**
    **讲解：** DHCP（Dynamic Host Configuration Protocol）自动为网络中的设备分配IP地址、子网掩码、网关和DNS等网络参数。域名解析由DNS负责，数据包路由由路由器负责。

15. 在RHEL 8中，哪个仓库提供了操作系统底层软件的核心集，其软件包生命周期与发行版一致？（ ）
    A. Application Stream (AppStream)
    B. BaseOS
    C. EPEL
    D. RPM Fusion
    **答案：B**
    **讲解：** BaseOS仓库包含操作系统核心基础软件包，生命周期与RHEL发行版完全一致。AppStream提供应用程序流（如不同版本的PHP/MySQL），EPEL是Fedora托管的额外包仓库，RPM Fusion提供第三方包。

16. 在Linux中，用于查看进程打开的文件、打开文件的进程、进程打开的端口（TCP、UDP）的命令是？（ ）
    A. ps
    B. top
    C. lsof
    D. netstat
    **答案：C**
    **讲解：** lsof（List Open Files）可以列出进程打开的文件描述符、网络连接和端口。例如`lsof -i :80`查看80端口的使用情况。ps查看进程快照，top动态监控资源，netstat查看网络连接统计。

17. 在Linux中，用于查看或设置系统环境变量的命令是？（ ）
    A. export
    B. env
    C. set
    D. echo
    **答案：A**
    **讲解：** `export`用于设置或显示环境变量。`export VAR=value`设置变量并使其在子进程中可用。`env`显示所有环境变量，`set`显示Shell所有变量（包括局部变量），`echo`输出变量值。

18. 以下哪个命令用于修改文件或目录的权限？（ ）
    A. chmod
    B. chown
    C. chgrp
    D. umask
    **答案：A**
    **讲解：** chmod（Change Mode）修改文件权限。chown修改属主，chgrp修改属组，umask设置默认权限掩码。

19. 在防火墙策略中，TCP Wrappers通过检查哪两个文件来控制对服务的访问？（假设服务支持TCP Wrappers）（ ）
    A. /etc/hosts.allow 和 /etc/hosts.deny
    B. /etc/ssh/sshd_config 和 /etc/ftp/ftpaccess
    C. /etc/sysconfig/iptables 和 /etc/firewalld/firewalld.conf
    D. /etc/samba/smb.conf 和 /etc/httpd/conf/httpd.conf
    **答案：A**
    **讲解：** TCP Wrappers按顺序检查：先看hosts.allow，匹配则放行；再看hosts.deny，匹配则拒绝；均不匹配则放行。

20. 在一个Shell脚本中，使用 varname=${param:+word} 进行有条件的变量替换。如果参数 param 已经被设置为一个非空值，那么变量 varname 会被设置成什么？（ ）
    A. 空值
    B. param 原来的值
    C. word 的值
    D. word 会被设置为 param 的值
    **答案：C**
    **讲解：** ${param:+word}的语义是：若param已设置且非空，则用word的值替换整个表达式。${param:-word}在param未设置时用word，${param:=word}在未设置时用word并赋值，${param:?word}在未设置时报错。

二、 多项选择题（每题2分，共20分）

1. 以下哪些命令可以用于创建新用户？（ ）
   A. useradd
   B. adduser
   C. passwd
   D. usermod
   **答案：A, B**
   **讲解：** useradd和adduser都可用于创建新用户（不同发行版有所差异，RHEL中两者均可）。passwd用于设置/修改密码，usermod用于修改已有用户的属性。

2. 使用ifconfig命令可以执行以下哪些操作？（ ）
   A. 查看网络接口状态
   B. 设置IP地址和子网掩码
   C. 激活或关闭网络接口
   D. 设置网关
   **答案：A, B, C**
   **讲解：** ifconfig可查看接口、设置IP地址/掩码、激活或关闭接口（ifconfig eth0 up/down）。设置网关需使用route命令或ip route，ifconfig无法直接设置网关。

3. 在bash中，有条件的变量替换语法包括（ ）
   A. 变量=${参数:-word}
   B. 变量=${参数:=word}
   C. 变量=${参数:?word}
   D. 变量=${参数:+word}
   **答案：A, B, C, D**
   **讲解：** 四种都是有条件的变量替换语法，参数展开时根据变量是否设置/为空执行不同操作。它们统称为Shell参数展开（Parameter Expansion）。

4. 使用RPM可以很容易地对RPM软件包进行哪些操作？（ ）
   A. 安装
   B. 升级
   C. 卸载
   D. 校验
   E. 查询
   **答案：A, B, C, D, E**
   **讲解：** RPM支持安装（-i）、升级（-U）、卸载（-e）、校验（-V）、查询（-q）等操作。RPM是底层包管理器，所有操作都直接作用于本地的rpm包文件。

5. 以下哪些是Linux支持的硬件平台？（ ）
   A. x86
   B. x64 (AMD64/Intel 64)
   C. ARM
   D. SPARC
   **答案：A, B, C, D**
   **讲解：** Linux支持几乎所有主流硬件架构，包括x86、x64、ARM（服务器/嵌入式）、SPARC（Sun/Oracle服务器）、MIPS、PowerPC、RISC-V等。

6. 以下哪些命令可以用于查看系统日志？（ ）
   A. dmesg
   B. journalctl
   C. cat /var/log/messages
   D. tail -f /var/log/syslog
   **答案：A, B, C, D**
   **讲解：** dmesg查看内核日志，journalctl查看systemd日志，/var/log/messages（RHEL）和/var/log/syslog（Debian）存放系统日志文件。tail -f可实时追踪日志输出。

7. 关于用户自定义变量，以下说法正确的是（ ）
   A. 定义语法：变量名=变量值
   B. 定义时变量名前不应加符号$
   C. 引用变量值时应在变量名前加$
   D. 等号两边可以有空格
   **答案：A, B, C**
   **讲解：** Shell变量定义语法为`变量名=值`，等号两边不能有空格（D错误）。定义时不加$（B正确），引用时使用$变量名访问值（C正确）。

8. YUM相对于RPM命令的主要优势体现在哪些方面？（ ）
   A. 自动解决软件包间的依赖关系
   B. 可以从网络仓库自动下载并安装软件
   C. 命令更简短易记
   D. 保留一个包含所有软件包信息的数据库
   **答案：A, B**
   **讲解：** YUM/DNF的核心优势是自动解决依赖从网络仓库下载安装。命令简短（C）和保留数据库（D）不是主要优势，RPM同样有数据库和简洁命令。

9. 以下哪些是Linux常见的文件系统类型？（ ）
   A. ext3
   B. ext4
   C. xfs
   D. ntfs
   **答案：A, B, C**
   **讲解：** ext3、ext4、xfs都是Linux原生文件系统。ntfs是Windows文件系统，Linux虽可读写ntfs但并非Linux常见文件系统类型（需额外驱动支持）。

10. 以下关于预定义变量$?的说法，正确的是（ ）
    A. 表示命令执行后返回的状态
    B. 即上一个命令的返回代码
    C. 值为0表示该命令正确执行
    D. 任何非0值表示命令出错
    **答案：A, B, C, D**
    **讲解：** $?保存上一个命令的退出码。0表示成功，非0（1-255）表示不同类型的错误或信号终止。Shell脚本中常用`if [ $? -eq 0 ]`判断命令执行结果。

三、 判断题（每题1分，共10分）

1. Linux只能在基于Intel x86系列CPU的计算机上运行。
   **答案：错**
   **讲解：** Linux支持几乎所有硬件架构，包括x86/x64、ARM、SPARC、PowerPC、RISC-V、MIPS等，是跨平台性最强的操作系统之一。

2. 使用ifup和ifdown命令可以激活和关闭网络接口，其效果与ifconfig enp8s0 up/down相同。
   **答案：对**
   **讲解：** ifup/ifdown是对网络接口的启用/停用，与ifconfig eth0 up/down效果相同。但ifup/ifdown会读取配置文件中的设置，而ifconfig直接操作接口。

3. 在Shell脚本中，以"#"开头的行都是注释。
   **答案：对**
   **讲解：** Shell脚本中"#"开头的行为注释（第一行`#!/bin/bash`中`#!`是shebang特殊标记，告诉系统使用哪个解释器执行，严格来说也是特殊注释）。

4. RPM判定一个文件是否是RPM格式，是根据文件扩展名是否为.rpm。
   **答案：错**
   **讲解：** RPM通过文件头（Header）中的魔数（Magic Number）和二进制结构来判断是否为RPM包格式，而非依赖扩展名。扩展名只是助记标识。

5. 在RHEL 8中，使用ifconfig命令进行的网络配置是永久生效的。
   **答案：错**
   **讲解：** RHEL 8中ifconfig配置的网络参数仅在当前会话生效，重启后丢失。若需永久配置，应使用nmcli或编辑/etc/sysconfig/network-scripts/下对应的配置文件。

6. 在定义变量时，变量名前应加符号$，在引用变量的内容时则不应在变量名前加$。
   **答案：错**
   **讲解：** 恰好相反——定义变量时不加$（如`name="Alice"`），引用变量时才加$（如`echo $name`）。这是Shell初学者最常见的错误之一。

7. YUM可以自动解决软件包间的依赖关系。
   **答案：对**
   **讲解：** YUM/DNF的核心特性就是自动解决依赖关系。当安装一个软件包时，YUM会自动查找并安装其所有依赖包，这是YUM相对于低级RPM工具的主要优势。

8. 在安装Linux时，必须创建单独的/home分区。
   **答案：错**
   **讲解：** 创建单独的分区是可选的。安装Linux时只需一个根分区（/）即可正常运行。但为方便管理和数据安全，推荐将/home、/var、/boot等分为独立分区。

9. 在Shell中，使用 command1 && command2，表示只有 command1 执行成功时，才会执行 command2。
   **答案：对**
   **讲解：** &&是逻辑AND运算符：当前一个命令退出码为0（成功）时，继续执行后一个命令。若前一个命令失败，则短路，后一个命令不执行。

10. firewalld的运行时配置（Runtime）是永久生效的，系统重启后不会丢失，修改配置后不需要重新加载。
    **答案：错**
    **讲解：** firewalld的运行时配置重启后丢失。若要永久生效，需使用`--permanent`参数并将配置重载（`firewall-cmd --reload`）。运行时配置适合临时测试，永久配置适合生产环境。

四、 简答题（每题5分，共25分）

1. （软件包管理/Shell） 请说明使用RPM和YUM/DNF管理软件包的主要区别。并写出使用YUM安装一个名为nginx的软件包，然后启动并设置其开机自启的命令序列。
   **答案与讲解：**
   区别：RPM是低级包管理器，直接操作.rpm文件，无法自动解决依赖关系。YUM/DNF是高级包管理器，基于仓库（Repository），可自动下载并解决依赖。
   命令序列：
   ```bash
   yum install nginx -y
   systemctl start nginx
   systemctl enable nginx
   ```

2. （文件系统/权限） 假设需要对一个目录 /shared/team 进行权限设置，要求：属主为manager，属组为teamgroup；teamgroup组成员拥有读写执行权限（7），目录内新建的文件继承父目录的属组；其他用户无任何权限（0）。请写出实现上述要求的完整命令。
   **答案与讲解：**
   ```bash
   groupadd teamgroup
   mkdir -p /shared/team
   chown manager:teamgroup /shared/team
   chmod 770 /shared/team        # 属主7+属组7+其他0
   chmod g+s /shared/team        # 设置setgid确保新建文件继承属组
   ```

3. （网络服务） 简述在RHEL 8中配置一个静态IP地址的两种主要方法（CLI）。
   **答案与讲解：**
   方法一（nmcli，推荐）：
   ```bash
   nmcli connection add type ethernet con-name static-eth0 ifname eth0
   nmcli connection modify static-eth0 ipv4.addresses 192.168.1.100/24
   nmcli connection modify static-eth0 ipv4.gateway 192.168.1.1
   nmcli connection modify static-eth0 ipv4.method manual
   nmcli connection up static-eth0
   ```
   方法二（编辑配置文件）：
   编辑`/etc/sysconfig/network-scripts/ifcfg-eth0`，设置BOOTPROTO=static、IPADDR=、NETMASK=、GATEWAY=、DNS1=，然后重启NetworkManager。

4. （Shell编程） 写出一个Shell脚本片段，检查参数指定的文件是否存在、是普通文件还是目录。
   **答案与讲解：**
   ```bash
   #!/bin/bash
   if [ -f "$1" ]; then
       echo "File $1 exists and is a regular file."
   elif [ -d "$1" ]; then
       echo "$1 is a directory."
   else
       echo "File $1 does not exist."
   fi
   ```
   -f测试是否为普通文件，-d测试是否为目录。$1是脚本第一个参数。注意$1使用双引号包裹防止文件名含空格导致错误。

5. （综合管理） 列举查看系统当前进程和系统资源（如CPU、内存）使用情况的至少三个命令，并简述其各自的特点或使用场景。
   **答案与讲解：**
   - **ps aux / ps -ef**：进程快照，查看当前所有进程的静态信息（PID、CPU%、内存%、启动时间等）。
   - **top / htop**：动态实时监控，自动刷新显示CPU和内存占用最高的进程。htop是top的增强版，支持鼠标操作和彩色显示。
   - **free -h**：查看内存使用情况（总内存、已用、空闲、缓存/缓冲）。配上`-h`以人类可读格式显示。

五、 计算题 (共10分)

假设一个文件的权限用符号法表示为 rwxr-xr-x。

1. 请写出该权限对应的八进制数字表示。 (2分)
   **答案：755**
   **讲解：** rwx=4+2+1=7（属主），r-x=4+0+1=5（属组），r-x=4+0+1=5（其他），故为755。

2. 使用 chmod 的数字法，写出将文件权限修改为 rw-r--r-- 的命令。(2分)
   **答案：** `chmod 644 文件名`
   **讲解：** rw-=4+2+0=6，r--=4+0+0=4，r--=4+0+0=4，即644。

3. 假设用户alice是属组staff的成员，但不是该文件的属主。该文件当前的符号法权限就是 rwxr-xr-x。
   a) alice 能否读取该文件？为什么？(2分)
   **答案：能。** alice是属组成员，权限r-x中第4-6位表示属组拥有读(r)和执行(x)权限。
   b) alice 能否修改（写入）该文件？为什么？(2分)
   **答案：不能。** 属组权限r-x中没有写(w)权限（rwx中的w仅针对属主）。
   c) alice 能否执行该文件？为什么？(2分)
   **答案：能。** 属组权限r-x中包含执行(x)权限，alice作为属组成员可以执行。

六、 应用题 (共15分)

场景：你是一名Linux系统管理员，需要为一台新部署的RHEL 8服务器配置基础服务环境。请根据要求完成以下任务：

1. 用户与权限管理（5分）：
   **答案与讲解：**
   ```bash
   groupadd -g 2500 webadmin
   useradd -g webadmin webuser
   echo "W3bP@ss!2024" | passwd --stdin webuser
   mkdir -p /var/www/webapp
   chown webuser:webadmin /var/www/webapp
   chmod 775 /var/www/webapp
   chmod g+s /var/www/webapp   # setgid位：新建文件自动继承webadmin组
   ```
   -g 2500指定GID，-g webadmin指定主要组。--stdin从管道读取密码。g+s（setgid=2）确保目录下新建文件自动继承父目录的属组。

2. Web服务配置与防火墙（7分）：
   **答案与讲解：**
   ```bash
   # 安装软件包
   yum install httpd firewalld -y

   # 修改DocumentRoot：编辑/etc/httpd/conf/httpd.conf
   # 将 DocumentRoot "/var/www/html" 改为 DocumentRoot "/var/www/webapp"
   # 同时将对应 <Directory "/var/www/html"> 改为 <Directory "/var/www/webapp">

   # 启动并设置开机自启
   systemctl start httpd
   systemctl enable httpd

   # 防火墙配置
   firewall-cmd --permanent --add-service=http
   firewall-cmd --permanent --add-service=https
   firewall-cmd --reload
   ```
   --permanent表示永久规则，reload使新规则生效。注意修改DocumentRoot后要确保SELinux上下文正确。

3. 自动化备份脚本（3分）：
   **答案与讲解：**
   ```bash
   #!/bin/bash
   BACKUP_DIR="/opt/backups"
   DATE=$(date +%Y%m%d)
   SOURCE_DIR="/var/www/webapp"
   BACKUP_FILE="${BACKUP_DIR}/webapp_backup_${DATE}.tar.gz"
   tar -zcf ${BACKUP_FILE} ${SOURCE_DIR} 2>/dev/null
   echo "Backup created: ${BACKUP_FILE}"
   ```
   `tar -zcf`：z=gzip压缩，c=创建归档，f=指定文件名。`date +%Y%m%d`生成日期字符串如20240115。`2>/dev/null`丢弃tar的警告信息。

《Linux操作系统》练习二

一、 单项选择题（每题1分，共20分）

1. 在Linux中，用于检查文件系统的磁盘空间占用情况的命令是？
   A. df
   B. du
   C. fdisk
   D. mount
   **答案：A**
   **讲解：** df（Disk Free）检查整个文件系统的磁盘空间使用情况。du统计目录/文件空间，fdisk分区管理，mount挂载文件系统。

2. 哪个命令用于修改用户密码？
   A. useradd
   B. passwd
   C. usermod
   D. groupadd
   **答案：B**
   **讲解：** passwd用于设置或修改用户密码。useradd创建用户，usermod修改用户属性，groupadd创建组。

3. Linux系统中存储用户账户信息（用户名、UID、GID等）但不包含加密密码的文件是？
   A. /etc/passwd
   B. /etc/shadow
   C. /etc/group
   D. /etc/login.defs
   **答案：A**
   **讲解：** /etc/passwd存储用户名、UID、GID、家目录、登录Shell等基本信息，所有用户可读。加密密码存储在/etc/shadow（仅root可读）。

4. 查看或编辑内核路由表的命令是？
   A. ifconfig
   B. route
   C. ping
   D. traceroute
   **答案：B**
   **讲解：** route命令查看和操作IP路由表。ifconfig配置接口，ping测试连通性，traceroute追踪路由路径。

5. RHEL 8中管理网络连接的核心服务是？
   A. network
   B. NetworkManager
   C. net-tools
   D. iproute2
   **答案：B**
   **讲解：** NetworkManager是RHEL 8/9默认网络管理服务。network是RHEL 6及之前的传统服务，net-tools和iproute2是底层工具集。

6. 以下哪个组合键用于命令行自动补全？
   A. Ctrl
   B. Alt
   C. Tab
   D. Shift
   **答案：C**
   **讲解：** Tab键是Shell中最常用的自动补全键，可补全命令、文件名、变量等。连续按两次Tab显示所有匹配项。

7. 使用crontab命令配置定时任务，希望每天凌晨2点30分执行一次脚本，正确的时间表达式是？
   A. `30 2 * * *`
   B. `2 30 * * *`
   C. `30 2 1 * *`
   D. `30 2 * * 0`
   **答案：A**
   **讲解：** crontab格式为"分 时 日 月 周"（5个字段）。`30 2 * * *`表示每天2:30执行。B中分钟和小时位置颠倒（2:30写成2 30），C的`1`表示每月1号，D的`0`表示周日（仅周日执行）。

8. 在Vim编辑器中，删除当前行并切换到输入模式应该按哪个键序列？
   A. dd
   B. cc
   C. xx
   D. dw
   **答案：B**
   **讲解：** `cc`是Vim中"删除当前行并进入插入模式"的命令。`dd`只删除不进入插入模式，`xx`删除一个字符，`dw`删除到单词末尾。

9. 用于查找名称为myapp的进程并终止它的命令是？
   A. kill myapp
   B. pkill myapp
   C. killall myapp
   D. 先ps aux | grep myapp找到PID，再用kill PID
   **答案：D**
   **讲解：** 本题需仔细审题——"查找并终止"需要两步。D描述了标准方法：先用ps|grep找到PID，再用kill终止。`kill`需要PID而非名称，`pkill`和`killall`虽可直接用名称，但不够精确（可能误杀同名进程）。

10. 查看系统当前登录用户信息的命令是？
    A. who
    B. w
    C. last
    D. whoami
    **答案：A**
    **讲解：** who显示当前登录系统的用户列表。w显示更详细的信息（含负载和进程），last显示历史登录记录，whoami仅显示当前用户的名称。

11. yum install命令在安装软件时，相比rpm -ivh的主要优势是？
    A. 安装速度更快
    B. 不需要root权限
    C. 能自动解决软件包依赖关系
    D. 命令更短
    **答案：C**
    **讲解：** YUM/DNF的核心优势是自动解决依赖关系并从仓库下载。RPM安装时若依赖缺失会直接报错退出。速度并非YUM优势（需下载），且两者都需要root权限。

12. 使用RPM查询一个已安装软件包（如vim-enhanced）所包含的所有文件列表，应使用哪个命令？
    A. rpm -qa vim-enhanced
    B. rpm -qi vim-enhanced
    C. rpm -ql vim-enhanced
    D. rpm -qf vim-enhanced
    **答案：C**
    **讲解：** `rpm -ql`（query list）列出已安装包的全部文件。`-qa`查询所有已安装包，`-qi`查询包信息，`-qf`查询某个文件属于哪个包。

13. Samba服务器的主配置文件是？
    A. /etc/samba/smb.conf
    B. /etc/smb.conf
    C. /etc/samba/smbd.conf
    D. /var/lib/samba/smb.conf
    **答案：A**
    **讲解：** Samba主配置文件是`/etc/samba/smb.conf`，定义了共享资源、安全选项和全局设置。

14. 在Apache配置文件（httpd.conf）中，用于指定网站文档根目录的指令是？
    A. ServerRoot
    B. DocumentRoot
    C. DirectoryIndex
    D. Listen
    **答案：B**
    **讲解：** DocumentRoot指定Apache提供网页文件的根目录。ServerRoot指定服务器安装目录，DirectoryIndex指定默认首页文件，Listen指定监听端口。

15. 在Shell脚本中，引用命令行第一个参数的变量是？
    A. $0
    B. $1
    C. $\*
    D. $$
    **答案：B**
    **讲解：** $1是第一个命令行参数。$0是脚本名称，$\*是所有参数（视为一个字符串），$$是当前进程的PID。

16. 使用chmod的数字法，将文件权限设置为rwxr-xr--，对应的数字是？
    A. 755
    B. 754
    C. 644
    D. 750
    **答案：B**
    **讲解：** rwx=4+2+1=7（属主），r-x=4+0+1=5（属组），r--=4+0+0=4（其他），即754。

17. 在RHEL 8中配置永久静态IP地址，可以使用以下哪种方法？（两种均可，但哪个是最佳实践）
    A. 仅使用 ifconfig 命令。
    B. 仅编辑 /etc/sysconfig/network-scripts/ifcfg-\* 文件。
    C. 使用 nmcli 命令修改连接，并设置ipv4.method为manual。
    D. 使用 ip addr add 命令。
    **答案：C**
    **讲解：** nmcli是RHEL 8/9推荐的最佳实践，可持久化配置。ifconfig和ip addr add的配置重启后丢失。编辑配置文件（B）虽可持久化，但nmcli更灵活、自动化友好。

18. 希望创建一个脚本，使其可以在任意路径下被执行，且能引用系统中定义的SHELL变量。最好的执行方式是？
    A. 直接输入脚本名（前提是路径在PATH中）
    B. bash script.sh
    C. source script.sh
    D. ./script.sh
    **答案：A**
    **讲解：** 将脚本放在PATH目录下（如/usr/local/bin）或修改PATH环境变量后，直接输入脚本名即可在任意路径执行。B（bash执行）和D（./执行）需要指定路径，C（source）虽保留变量环境但需指定路径。

19. 在Shell脚本中，判断变量VAR是否已设置且不为空的正确测试方法是？（双引号的使用很重要）
    A. if [ $VAR != "" ]; then
    B. if [ -n "$VAR" ]; then
    C. if [ ! -z $VAR ]; then
    D. if test -n $VAR; then
    **答案：B**
    **讲解：** `[ -n "$VAR" ]`判断VAR长度是否非零，双引号包裹防止变量为空时语法错误。A中若VAR为空展开为`[ != "" ]`语法错误。C和D缺少双引号，变量为空时出错。

20. 当执行一个RPM包的升级（rpm -Uvh package.rpm）时，如果该软件包之前从未安装过，此命令会如何处理？
    A. 报错退出。
    B. 自动转换为安装操作。
    C. 询问用户是否要安装。
    D. 仅升级相关的配置文件。
    **答案：B**
    **讲解：** rpm的-U（upgrade）模式具有"安装或升级"语义：若已安装则升级，若未安装则自动安装。若希望仅升级不安装，需先检查是否已安装。

二、 多项选择题（每题2分，共20分）

1. 以下哪些命令属于 iproute2 新工具包，用于替代 net-tools 旧工具包？（ ）
   A. ifconfig -> ip addr
   B. route -> ip route
   C. netstat -> ss
   D. arp -> ip neigh
   **答案：A, B, C, D**
   **讲解：** iproute2全面替代net-tools：ip addr替代ifconfig，ip route替代route，ss替代netstat，ip neigh替代arp。建议新系统使用iproute2系列命令。

2. 关于ps命令，以下说法正确的是？（ ）
   A. ps aux：显示所有用户的所有进程详细信息。
   B. ps -ef：使用标准格式列出所有进程。
   C. 显示进程的树状结构应使用pstree。
   D. 要动态观察进程状态，使用top或htop。
   **答案：A, B, C, D**
   **讲解：** ps aux和ps -ef是最常用的两个进程查看格式。pstree以树状展示父子关系。top/htop动态实时刷新显示。

3. 关于find命令，以下哪些参数或操作是正确的？（ ）
   A. -name：按文件名查找。
   B. -type：按文件类型（如f、d）查找。
   C. -exec：对查找到的文件执行后续命令。
   D. -size：按文件大小查找。
   **答案：A, B, C, D**
   **讲解：** find常用参数：-name按名称、-type按类型（f文件/d目录/l链接）、-exec执行命令、-size按大小（如+100M）。多个条件可组合使用。

4. 用户自定义变量赋值时，正确的规则是？（ ）
   A. 变量名=值（等号两边不能有空格）。
   B. 值中有空格，需要用引号引起来。
   C. 引用变量值时，应在变量名前加$。
   D. 定义时，应在变量名前加$。
   **答案：A, B, C**
   **讲解：** Shell变量定义语法：`变量名=值`，等号两侧不能有空格。值含空格需引号。定义不加$，引用加$。D恰好说反。

5. 以下哪些是关于YUM仓库配置的正确描述？（ ）
   A. 配置文件存储在 /etc/yum.repos.d/ 目录下，后缀为 .repo。
   B. enabled=1 表示启用此仓库。
   C. gpgcheck=1 表示启用GPG签名检查。
   D. baseurl 指定了仓库的URL地址，可支持 http://, ftp://, file://。
   **答案：A, B, C, D**
   **讲解：** YUM仓库配置的四个核心字段：baseurl指定URL、enabled控制启用、gpgcheck控制签名检查、name指定名称。配置文件以.repo结尾放在/etc/yum.repos.d/下。

6. SELinux的主要运行模式有哪几种？（ ）
   A. Enforcing：强制执行安全策略。
   B. Permissive：记录违规但不阻止。
   C. Disabled：完全禁用。
   D. Audit：仅进行审计。
   **答案：A, B, C**
   **讲解：** SELinux有三种模式：Enforcing（强制）、Permissive（宽容/仅记录）、Disabled（禁用）。D中的"Audit"不是独立模式，Permissive模式下即执行审计记录。

7. Apache HTTP 服务器中可以实现基于以下哪些方式的虚拟主机？（ ）
   A. 基于IP地址。
   B. 基于端口号。
   C. 基于主机名（域名）。
   D. 基于用户认证。
   **答案：A, B, C**
   **讲解：** Apache支持三种虚拟主机方式：基于IP（不同IP不同站点）、基于端口（不同端口不同站点）、基于名称（不同域名同一IP），其中基于名称最常用。D的用户认证是独立功能，非虚拟主机类型。

8. DHCP客户端与服务器交互获取IP地址的过程，正确的报文顺序是？（ ）
   A. DISCOVER, OFFER, REQUEST, ACK
   B. OFFER, DISCOVER, REQUEST, ACK
   C. DISCOVER, REQUEST, OFFER, ACK
   D. 首先由客户端广播DHCPDISCOVER。
   **答案：A, D**
   **讲解：** DHCP标准交互过程（DORA）：客户端广播DISCOVER → 服务器单播OFFER → 客户端广播REQUEST → 服务器ACK确认。D描述了第一步也是正确的。B和C顺序错误。

9. 关于Firewalld和IPTables的关系，以下说法正确的是？（ ）
   A. Firewalld是RHEL 7/8中的默认防火墙管理工具。
   B. Firewalld可以提供动态配置，无需重启服务即可应用新规则。
   C. Firewalld默认后端在RHEL 8中是nftables，但RHEL 7可能使用iptables。
   D. iptables命令已完全被淘汰，无法在RHEL 8中使用。
   **答案：A, B, C**
   **讲解：** Firewalld是RHEL 7+默认防火墙，支持动态运行时配置。RHEL 8中Firewalld后端默认为nftables（RHEL 7为iptables）。iptables命令在RHEL 8中仍可使用（通过兼容层），D错误。

10. 关于网络地址转换（NAT）与防火墙，以下说法正确的是？（ ）
    A. iptables 的 nat 表用于实现网络地址转换。
    B. firewalld 是 RHEL 7/8 中默认的防火墙管理工具。
    C. firewalld 的后端可以是 iptables 或 nftables。
    D. 防火墙规则不仅可以过滤数据包，还可以修改数据包的地址（NAT）。
    **答案：A, B, C, D**
    **讲解：** iptables有filter（过滤）、nat（地址转换）、mangle（修改）等表。Firewalld是RHEL 7/8默认防火墙，后端可配置为iptables或nftables。NAT功能属于地址修改而非简单过滤。

三、 判断题（每题1分，共10分）

1. rmdir命令可以删除非空目录。
   **答案：错**
   **讲解：** rmdir只能删除空目录。删除非空目录应使用`rm -rf`命令。

2. du命令用于统计文件和目录占用的磁盘空间大小。
   **答案：对**
   **讲解：** du（Disk Usage）统计文件/目录的磁盘使用量。`du -sh`查看目录总大小，`du -h`查看各文件大小。

3. 在Shell中，$?变量的值表示上一个命令的退出状态，0通常代表成功。
   **答案：对**
   **讲解：** $?是Shell预定义变量，保存上一个命令的退出码。0表示成功，非0表示不同类型的错误。

4. 使用rpm -e卸载软件包时，如果存在依赖关系，会强制卸载。
   **答案：错**
   **讲解：** 默认情况下rpm -e会检查依赖关系，若其他已安装包依赖该包则拒绝卸载，报错退出。`--nodeps`参数可跳过依赖检查（但不推荐）。

5. Samba服务器使用139和445端口进行文件共享通信。
   **答案：对**
   **讲解：** Samba使用TCP 139（NetBIOS会话服务）和445（SMB over TCP）端口提供文件共享和打印服务。

6. TCP Wrappers通过检查/etc/hosts.allow和/etc/hosts.deny来实现访问控制，检查顺序是先hosts.deny，再hosts.allow。
   **答案：错**
   **讲解：** TCP Wrappers检查顺序是hosts.allow优先于hosts.deny。先在allow中查找，匹配则放行；不匹配再查deny。先allow后deny，allow比deny优先级高。

7. 在使用Vim编辑器时，输入:wq可以保存文件并退出。
   **答案：对**
   **讲解：** :wq在Vim末行模式中表示"写入（write）并退出（quit）"。等效命令有`:x`和`ZZ`（命令模式下）。

8. systemctl status firewalld命令用于查看firewalld服务的当前状态。
   **答案：对**
   **讲解：** systemctl status显示服务的运行状态、PID、最近日志等信息。加`-l`参数可显示完整输出。

9. 使用useradd命令时，如果不加-m参数，默认不会创建用户的主目录。
   **答案：对**
   **讲解：** RHEL中useradd默认创建家目录（不依赖-m参数）。但其他发行版行为可能不同，在RHEL中该表述实为正确的（useradd默认确实创建家目录）。**注意：** 原参考答案标的"对"，本题在RHEL环境下正确。

10. 在Linux权限管理中，为目录设置setgid位（如chmod g+s dir）后，任何用户在此目录下创建的新文件，其属组都会继承该目录的属组。
    **答案：对**
    **讲解：** setgid（SGID）位的作用：对目录设置后，在该目录下创建的文件/子目录自动继承目录的属组而非创建者的主要组。`chmod g+s`或数字法`2`前缀设置。

四、 简答题（每题5分，共25分）

1. （系统管理） 简述/etc/passwd文件和/etc/shadow文件各自的用途及区别。
   **答案与讲解：**
   用途：
   - /etc/passwd：存储用户账户基本信息（用户名、UID、GID、家目录、登录Shell），所有用户可读。
   - /etc/shadow：存储加密密码及安全策略（密码过期天数、失效时间等），仅root可读。
   区别：/etc/passwd的密码字段在现代Linux中为`x`或`*`占位，实际加密密码移至/etc/shadow。这种分离增强了安全性。

2. （系统命令） 请分别写出完成以下功能的命令：
   a) 查看/var/log/messages文件的最新10行内容，并持续监控其新增内容。
   b) 查找系统中所有在过去24小时内修改过的普通文件，并以列表形式输出详细信息。
   **答案与讲解：**
   a) `tail -f /var/log/messages` 或 `tail -10f /var/log/messages`。`-f`（follow）持续追踪文件新增内容。
   b) `find / -type f -mtime -1 -ls`。`-type f`普通文件，`-mtime -1`表示1天内（24小时内）修改过，`-ls`以列表格式输出。

3. （网络配置） 在RHEL 8中，有一个网络接口enp0s3，请写出使用nmcli命令将其IP地址设置为静态地址192.168.1.100/24，网关为192.168.1.1，DNS服务器为8.8.8.8和114.114.114.114的基本命令步骤。（假设连接名为"有线连接 1"）
   **答案与讲解：**
   ```bash
   nmcli connection modify "有线连接 1" ipv4.addresses 192.168.1.100/24
   nmcli connection modify "有线连接 1" ipv4.gateway 192.168.1.1
   nmcli connection modify "有线连接 1" ipv4.dns "8.8.8.8 114.114.114.114"
   nmcli connection modify "有线连接 1" ipv4.method manual
   nmcli connection up "有线连接 1"
   ```
   注意连接名含空格需用引号包裹。`ipv4.method manual`表示手动（静态）配置，最后需激活连接。

4. （软件包管理） 小张有一台没有接入互联网的RHEL 8服务器。他手头有一张RHEL 8的安装光盘。请简要说明他如何利用这张光盘创建一个本地YUM仓库，并使用此仓库安装httpd软件包。
   **答案与讲解：**
   ```bash
   mount /dev/cdrom /mnt                 # 挂载光盘
   yum install createrepo -y             # 安装createrepo（若未安装）
   createrepo /mnt                       # 创建仓库元数据
   ```
   配置/etc/yum.repos.d/local.repo：
   ```
   [LocalRepo]
   name=Local Repository
   baseurl=file:///mnt
   enabled=1
   gpgcheck=0
   ```
   最后执行：
   ```bash
   yum clean all && yum makecache
   yum install httpd -y
   ```

5. （进程与服务管理） 请写出完成以下操作的systemctl命令：
   a) 启动httpd服务。
   b) 停止firewalld服务。
   c) 设置sshd服务开机自启。
   d) 查看NetworkManager服务的实时状态（包含运行日志）。
   e) 重新加载sshd服务的配置文件，不重启服务（如果支持）。
   **答案与讲解：**
   ```bash
   a) systemctl start httpd
   b) systemctl stop firewalld
   c) systemctl enable sshd
   d) systemctl status -l NetworkManager
   e) systemctl reload sshd
   ```
   start/stop控制运行状态，enable/disable控制开机自启，status查看状态（-l完整输出），reload热加载配置。

五、 计算题 (共10分)

有一个新建项目团队teamA，项目目录为/data/projectA。团队成员user1和user2都需要能读写目录内的文件并创建新文件。同时，user1是项目负责人，需要能够设置文件权限。

1. 创建组teamA，并将user1和user2添加至teamA组的命令。（2分）
   **答案：**
   ```bash
   groupadd teamA
   usermod -aG teamA user1
   usermod -aG teamA user2
   ```
   **讲解：** -aG是追加到补充组（不会移除已有组）。注意：不加-G参数会修改用户主要组而非追加。

2. 创建/data/projectA目录，设置其属组为teamA，权限为775（rwxrwxr-x）。写出命令。（2分）
   **答案：**
   ```bash
   mkdir -p /data/projectA
   chown :teamA /data/projectA
   chmod 775 /data/projectA
   ```
   **讲解：** `chown :teamA`只改属组不改属主（省略属主部分），等效于`chgrp teamA`。

3. 希望保证无论谁在/data/projectA目录下新建文件，其所属组都是teamA。需要额外添加一个特殊权限位。请写出对应的chmod命令（使用数字法）。（2分）
   **答案：** `chmod 2775 /data/projectA`
   **讲解：** 数字法中的前缀`2`表示设置setgid位。2755=setgid(2)+rwx(7)+rwx(7)+r-x(5)。之后该目录下新建的文件自动继承teamA属组。

4. user1创建了脚本script.sh（初始权限644）。为了让teamA组成员都能执行此脚本，修改权限的命令（字母法和数字法各写一次）。（2分）
   **答案：**
   ```bash
   # 字母法
   chmod g+x /data/projectA/script.sh
   # 数字法
   chmod 774 /data/projectA/script.sh
   ```
   **讲解：** 644=rw-r--r--，给组加执行权限后变为rwxrw-r--（数字法计算：6+0=6不变，4+1=5变7，故为774）。

5. 将/data/projectA目录权限从777改为撤销其他用户（Other）的写（w）和执行（x）权限后的值（数字法），并写出chmod命令。（2分）
   **答案：** `chmod 755 /data/projectA`
   **讲解：** 777=rwxrwxrwx，撤销Other的wx（去掉2+1=3）后，Other部分由7变为5(r-x)，即755。其他用户仅保留读和执行权限。

六、 应用题 (共15分)

你是某公司的Linux系统管理员，需要为一台新的应用服务器（RHEL 8）配置基础环境，确保其上运行的Web应用安全稳定。请完成以下任务：

1. 安全与目录配置（6分）
   **答案与讲解：**
   a) `useradd -r -M -d /srv/webapp -s /sbin/nologin webapp`（1分）
      -r创建系统用户（UID<1000），-M不创建家目录，-d指定家目录路径，-s指定不可登录Shell（/sbin/nologin）。
   b) 
   ```bash
   mkdir -p /var/www/html/app
   chown webapp:webapp /var/www/html/app
   chmod 750 /var/www/html/app
   ```（2分）
   c) 目录：777-022=755(rwxr-xr-x)，文件：666-022=644(rw-r--r--)（1分）
      注意：文件默认无执行权限，故从666而非777开始减。
   d) `ls -Zd /var/www/html/app`查看SELinux上下文，`setenforce 0`临时切换为Permissive模式（1分）
      -Z参数显示SELinux上下文，getenforce可查看当前模式。

2. 服务部署与访问控制（6分）
   **答案与讲解：**
   a) `yum install httpd firewalld -y`（1分）
   b) 
   ```bash
   firewall-cmd --permanent --add-service=http
   firewall-cmd --permanent --add-service=https
   firewall-cmd --reload
   ```（2分）
   --permanent写入永久配置，reload加载使规则生效。
   c) `sshd : 192.168.10.0/24`写入/etc/hosts.allow（1分）
      格式：`服务名 : 允许地址`。这里允许192.168.10.0/24网段SSH访问，其余默认拒绝（需在hosts.deny设ALL:ALL）。
   d) 原因：脚本没有可执行权限（1分）
      解决方案（任选二）：
      - `chmod +x /usr/local/bin/check_service.sh`添加执行权限
      - 使用`bash /usr/local/bin/check_service.sh`或`source`执行
      - 检查脚本所属SELinux上下文（ls -Z）（各1分，共2分）

3. Shell脚本编写与自动化（3分）
   **答案与讲解：**
   ```bash
   #!/bin/bash
   SOURCE_DIR="/var/www/html/app"
   BACKUP_DIR="/backup"
   TIMESTAMP=$(date +%Y-%m-%d)
   BACKUP_FILE="${BACKUP_DIR}/app_backup_${TIMESTAMP}.tar.gz"
   tar -zcf ${BACKUP_FILE} ${SOURCE_DIR} 2>/dev/null
   ```
   `date +%Y-%m-%d`生成日期字符串如2024-01-15，`tar -zcf`压缩归档。`2>/dev/null`丢弃无关警告。