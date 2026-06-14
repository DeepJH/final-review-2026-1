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
| `$?` | 上一个命令的退出状态（0=成功，非0=失败） |  
| `$!` | 后台最后一个进程的PID |  
| `$0` | 当前执行的进程名 |  
| `$1` | 脚本第一个参数 |  
| `$#` | 参数个数 |  
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

<br>
<br>
<br>
<br>
<br>
<br>
《Linux操作系统》练习一  
<br>
<br>
<br>
<br>
<br>
<br>
一、 单项选择题（每题1分，共20分）  
<br>
<br>
<br>
<br>
<br>
<br>
1. Linux操作系统的起源与哪位开发者紧密相关？（ ）  
   A. Bill Gates  
   B. Steve Jobs  
   C. Linus Torvalds  
   D. Richard Stallman  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：C**  
   **讲解：**  
   A. Bill Gates：错误。比尔·盖茨是微软创始人，与Linux内核开发无直接关系。  
   B. Steve Jobs：错误。史蒂夫·乔布斯是苹果创始人，与Linux内核开发无直接关系。  
   C. Linus Torvalds：正确。Linux内核由Linus Torvalds于1991年首次发布。  
   D. Richard Stallman：错误。他是GNU项目发起人，提供了大量用户态工具，但Linux内核本身由Torvalds编写。  
<br>
<br>
<br>
<br>
<br>
<br>
2. 在Linux中，root用户的UID是？（ ）  
   A. 1  
   B. 0  
   C. 1000  
   D. 999  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：B**  
   **讲解：**  
   A. 1：错误。UID 1通常分配给bin等系统用户账户，非root。  
   B. 0：正确。root是超级管理员，UID固定为0，拥有系统最高权限。  
   C. 1000：错误。UID 1000通常是RHEL中第一个普通用户的默认UID。  
   D. 999：错误。UID 999常用于系统用户或服务账户，非root。  
<br>
<br>
<br>
<br>
<br>
<br>
3. 在RHEL 8中，默认的包管理器是？（ ）  
   A. YUM  
   B. DNF  
   C. dpkg  
   D. apt  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：B**  
   **讲解：**  
   A. YUM：错误。RHEL 8中`yum`命令虽仍可使用（通过软链接指向DNF），但底层默认包管理器已更换为DNF。  
   B. DNF：正确。RHEL 8默认使用DNF，提供更快的依赖解析和更好的API支持。  
   C. dpkg：错误。dpkg是Debian/Ubuntu系列的底层包管理器，RHEL使用RPM体系。  
   D. apt：错误。apt是Debian/Ubuntu系列的高级包管理器，不用于RHEL系统。  
<br>
<br>
<br>
<br>
<br>
<br>
4. 在Shell中，用于获取上一个命令执行后返回的状态（退出代码）的预定义变量是？（ ）  
   A. `$!`  
   B. `$?`  
   C. `$0`  
   D. `$_`  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：B**  
   **讲解：**  
   A. `$!`：错误。`$!`保存最后一个后台进程的PID，非退出状态码。  
   B. `$?`：正确。`$?`保存上一个命令的退出状态码，0代表成功，非0代表失败。  
   C. `$0`：错误。`$0`保存当前执行的进程名或脚本名称。  
   D. `$_`：错误。`$_`是Shell的最后一个参数变量（上一条命令的最后一个参数）。  
<br>
<br>
<br>
<br>
<br>
<br>
5. 在RHEL 8中，用于管理网络连接、检测网络、自动连接网络的程序是？（ ）  
   A. network  
   B. NetworkManager  
   C. net-tools  
   D. iproute2  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：B**  
   **讲解：**  
   A. network：错误。network是RHEL 6及之前版本使用的传统网络服务，RHEL 8中已被NetworkManager取代。  
   B. NetworkManager：正确。NetworkManager是RHEL 8/9中默认的网络管理服务。  
   C. net-tools：错误。net-tools（ifconfig等）是底层工具集，提供网络接口配置功能，但不能作为持续运行的服务。  
   D. iproute2：错误。iproute2（ip命令等）同样是底层工具集，不提供网络管理服务功能。  
<br>
<br>
<br>
<br>
<br>
<br>
6. 使用userdel命令删除用户时，若要同时删除用户的主目录，应使用哪个参数？（ ）  
   A. -r  
   B. -d  
   C. -m  
   D. -f  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A**  
   **讲解：**  
   A. -r：正确。userdel -r表示递归删除用户账户及其主目录和邮件池。  
   B. -d：错误。userdel无-d选项，-d是usermod的选项（修改家目录路径）。  
   C. -m：错误。userdel无-m选项，-m是useradd的选项（创建用户时同时创建家目录）。  
   D. -f：错误。userdel -f表示强制删除（即使用户还在登录），但不删除主目录。  
<br>
<br>
<br>
<br>
<br>
<br>
7. 在Linux中，用于检查文件系统的磁盘空间占用情况的命令是？（ ）  
   A. df  
   B. du  
   C. lsblk  
   D. fdisk  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A**  
   **讲解：**  
   A. df：正确。df（Disk Free）显示文件系统整体的磁盘空间使用情况。  
   B. du：错误。du（Disk Usage）统计单个目录或文件占用的空间，不显示文件系统整体使用情况。  
   C. lsblk：错误。lsblk查看块设备信息（磁盘分区布局），不显示空间使用量。  
   D. fdisk：错误。fdisk用于管理磁盘分区表，不显示文件系统空间使用情况。  
<br>
<br>
<br>
<br>
<br>
<br>
8. 在RHEL 8中，默认的Web服务器软件是？（ ）  
   A. Nginx  
   B. Apache HTTP Server  
   C. Tomcat  
   D. Lighttpd  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：B**  
   **讲解：**  
   A. Nginx：错误。Nginx在RHEL上需额外安装，非默认Web服务器。  
   B. Apache HTTP Server：正确。RHEL 8/9默认使用Apache HTTP Server（httpd）作为Web服务器。  
   C. Tomcat：错误。Tomcat是Java Servlet/JSP容器，非RHEL默认的Web服务器。  
   D. Lighttpd：错误。Lighttpd是轻量级Web服务器，需额外安装部署。  
<br>
<br>
<br>
<br>
<br>
<br>
9. 在Vim编辑器中，从命令模式切换到输入模式，应按哪个键？（ ）  
   A. Esc  
   B. i  
   C. :  
   D. v  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：B**  
   **讲解：**  
   A. Esc：错误。Esc是从输入模式返回命令模式，而非进入输入模式。  
   B. i：正确。按`i`键从命令模式切换到输入模式（insert），可编辑文本。  
   C. :：错误。:进入末行模式（ex模式），用于执行保存、退出等命令。  
   D. v：错误。v进入可视模式（visual），用于选中文本块。  
<br>
<br>
<br>
<br>
<br>
<br>
10. 使用RPM命令安装软件包时，所用的选项是？（ ）  
    A. -i  
    B. -e  
    C. -U  
    D. -q  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A**  
    **讲解：**  
    A. -i：正确。`rpm -i`（install）用于安装新的RPM软件包。  
    B. -e：错误。`rpm -e`（erase）用于卸载已安装的软件包。  
    C. -U：错误。`rpm -U`（upgrade）用于升级软件包（已安装则升级，未安装则安装）。  
    D. -q：错误。`rpm -q`（query）用于查询已安装的软件包信息。  
<br>
<br>
<br>
<br>
<br>
<br>
11. 在Linux中，用于引导操作系统内核的程序通常被称为？（ ）  
    A. Kernel Loader  
    B. Bootloader  
    C. System Initializer  
    D. Startup Manager  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：B**  
    **讲解：**  
    A. Kernel Loader：错误。无名为"Kernel Loader"的标准术语，加载内核是Bootloader的一个功能步骤。  
    B. Bootloader：正确。Bootloader是系统启动时第一个运行的程序，负责加载操作系统内核到内存并启动系统。  
    C. System Initializer：错误。系统初始化由init/systemd完成，在Bootloader加载内核之后执行。  
    D. Startup Manager：错误。没有以"Startup Manager"命名的标准启动组件。  
<br>
<br>
<br>
<br>
<br>
<br>
12. 在Linux中，用于查看或编辑内核路由表的命令是？（ ）  
    A. ifconfig  
    B. route  
    C. dhclient  
    D. traceroute  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：B**  
    **讲解：**  
    A. ifconfig：错误。ifconfig配置和查看网络接口参数（IP地址、掩码等），不操作路由表。  
    B. route：正确。route命令用于查看和操作内核IP路由表。现代替代是`ip route`命令。  
    C. dhclient：错误。dhclient用于从DHCP服务器获取动态IP地址，不涉及路由表。  
    D. traceroute：错误。traceroute追踪数据包到达目标的路径，不查看或修改路由表。  
<br>
<br>
<br>
<br>
<br>
<br>
13. 在Shell中，用于显示当前执行的进程名的预定义变量是？（ ）  
    A. `$!`  
    B. `$?`  
    C. `$0`  
    D. `$$`  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：C**  
    **讲解：**  
    A. `$!`：错误。`$!`保存最后一个后台进程的PID编号。  
    B. `$?`：错误。`$?`保存上一个命令的退出状态码。  
    C. `$0`：正确。`$0`保存当前脚本或进程的名称。  
    D. `$$`：错误。`$$`保存当前进程的PID编号。  
<br>
<br>
<br>
<br>
<br>
<br>
14. DHCP协议的主要目的是？（ ）  
    A. 动态分配IP地址  
    B. 解析域名  
    C. 路由数据包  
    D. 提供Web服务  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A**  
    **讲解：**  
    A. 动态分配IP地址：正确。DHCP自动为设备分配IP地址及其他网络参数（子网掩码、网关、DNS等）。  
    B. 解析域名：错误。域名解析由DNS（Domain Name System）服务完成。  
    C. 路由数据包：错误。数据包路由由路由器和内核路由表完成。  
    D. 提供Web服务：错误。Web服务由Apache、Nginx等HTTP服务器提供。  
<br>
<br>
<br>
<br>
<br>
<br>
15. 在RHEL 8中，哪个仓库提供了操作系统底层软件的核心集，其软件包生命周期与发行版一致？（ ）  
    A. Application Stream (AppStream)  
    B. BaseOS  
    C. EPEL  
    D. RPM Fusion  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：B**  
    **讲解：**  
    A. AppStream：错误。AppStream提供应用程序及多版本支持（如PHP 7.x/8.x），非操作系统底层核心。  
    B. BaseOS：正确。BaseOS包含操作系统核心基础软件包，生命周期与RHEL发行版完全一致。  
    C. EPEL：错误。EPEL是Fedora项目托管的额外包仓库，提供RHEL官方不包含的软件包。  
    D. RPM Fusion：错误。RPM Fusion提供第三方软件包（如多媒体解码器），非RHEL官方仓库。  
<br>
<br>
<br>
<br>
<br>
<br>
16. 在Linux中，用于查看进程打开的文件、打开文件的进程、进程打开的端口（TCP、UDP）的命令是？（ ）  
    A. ps  
    B. top  
    C. lsof  
    D. netstat  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：C**  
    **讲解：**  
    A. ps：错误。ps查看当前运行进程的快照信息（PID、CPU%等），不显示文件打开或端口占用情况。  
    B. top：错误。top动态显示进程资源占用（CPU/内存），不提供文件句柄或端口信息。  
    C. lsof：正确。lsof（List Open Files）列出进程打开的文件描述符、网络连接和端口。如`lsof -i :80`查看80端口占用。  
    D. netstat：错误。netstat可查看网络连接统计，但不如lsof能精确列出每个进程打开的端口。  
<br>
<br>
<br>
<br>
<br>
<br>
17. 在Linux中，用于查看或设置系统环境变量的命令是？（ ）  
    A. export  
    B. env  
    C. set  
    D. echo  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A**  
    **讲解：**  
    A. export：正确。export用于设置环境变量，使其在当前Shell及子进程中可用。`export VAR=value`是最常用的用法。  
    B. env：错误。env仅显示当前所有环境变量，不能用于设置变量。  
    C. set：错误。set显示Shell的所有变量（包含局部变量和环境变量），但不能专门设置环境变量。  
    D. echo：错误。echo用于输出变量值（`echo $VAR`），不能设置或管理环境变量。  
<br>
<br>
<br>
<br>
<br>
<br>
18. 以下哪个命令用于修改文件或目录的权限？（ ）  
    A. chmod  
    B. chown  
    C. chgrp  
    D. umask  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A**  
    **讲解：**  
    A. chmod：正确。chmod（Change Mode）修改文件或目录的读/写/执行权限。  
    B. chown：错误。chown（Change Owner）修改文件或目录的属主和属组。  
    C. chgrp：错误。chgrp（Change Group）仅修改文件或目录的属组。  
    D. umask：错误。umask设置新建文件或目录的默认权限掩码，不修改已有文件的权限。  
<br>
<br>
<br>
<br>
<br>
<br>
19. 在防火墙策略中，TCP Wrappers通过检查哪两个文件来控制对服务的访问？（假设服务支持TCP Wrappers）（ ）  
    A. /etc/hosts.allow 和 /etc/hosts.deny  
    B. /etc/ssh/sshd_config 和 /etc/ftp/ftpaccess  
    C. /etc/sysconfig/iptables 和 /etc/firewalld/firewalld.conf  
    D. /etc/samba/smb.conf 和 /etc/httpd/conf/httpd.conf  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A**  
    **讲解：**  
    A. hosts.allow和hosts.deny：正确。TCP Wrappers顺序检查：hosts.allow匹配则放行；hosts.deny匹配则拒绝；均不匹配则默认放行。  
    B. sshd_config和ftpaccess：错误。这些是SSH和FTP服务自身的配置文件，与TCP Wrappers机制无关。  
    C. iptables和firewalld：错误。这些是防火墙（netfilter/firewalld）的配置文件，属于OSI第3/4层访问控制，TCP Wrappers工作在应用层。  
    D. smb.conf和httpd.conf：错误。这些是Samba和Apache的应用程序配置文件，与TCP Wrappers无关。  
<br>
<br>
<br>
<br>
<br>
<br>
20. 在一个Shell脚本中，使用 `varname=${param:+word}` 进行有条件的变量替换。如果参数 param 已经被设置为一个非空值，那么变量 varname 会被设置成什么？（ ）  
    A. 空值  
    B. param 原来的值  
    C. word 的值  
    D. word 会被设置为 param 的值  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：C**  
    **讲解：**  
    A. 空值：错误。当param已设置且非空时，`${param:+word}`返回word的值而非空值（只有param未设置或为空时才返回空）。  
    B. param原值：错误。`${param:+word}`返回word而非param的值。`${param:-word}`才在param未设置时返回word。  
    C. word的值：正确。当param已设置且非空时，整个表达式展开为word的值。  
    D. word会被设置为param的值：错误。该表达式不改变任何变量值，只是条件替换展开。`${param:=word}`才会赋值。  
<br>
<br>
<br>
<br>
<br>
<br>
二、 多项选择题（每题2分，共20分）  
<br>
<br>
<br>
<br>
<br>
<br>
1. 以下哪些命令可以用于创建新用户？（ ）  
   A. useradd  
   B. adduser  
   C. passwd  
   D. usermod  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A, B**  
   **讲解：**  
   A. useradd：正确。useradd是Linux创建新用户的标准命令（RHEL系列）。  
   B. adduser：正确。adduser也是用户创建命令，在某些发行版中是useradd的符号链接或封装脚本。  
   C. passwd：错误。passwd用于设置或修改用户密码，不能创建新用户。  
   D. usermod：错误。usermod用于修改已有用户的属性（如家目录、UID），不能创建新用户。  
<br>
<br>
<br>
<br>
<br>
<br>
2. 使用ifconfig命令可以执行以下哪些操作？（ ）  
   A. 查看网络接口状态  
   B. 设置IP地址和子网掩码  
   C. 激活或关闭网络接口  
   D. 设置网关  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A, B, C**  
   **讲解：**  
   A. 查看网络接口状态：正确。ifconfig可查看所有网络接口的IP地址、MAC地址、收发数据包等信息。  
   B. 设置IP地址和子网掩码：正确。`ifconfig eth0 192.168.1.1 netmask 255.255.255.0`可设置IP和掩码。  
   C. 激活或关闭网络接口：正确。`ifconfig eth0 up/down`可激活或关闭指定网络接口。  
   D. 设置网关：错误。设置网关需使用route或`ip route add default gw 192.168.1.1`，ifconfig不支持此功能。  
<br>
<br>
<br>
<br>
<br>
<br>
3. 在bash中，有条件的变量替换语法包括（ ）  
   A. 变量=`${参数:-word}`  
   B. 变量=`${参数:=word}`  
   C. 变量=`${参数:?word}`  
   D. 变量=`${参数:+word}`  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A, B, C, D**  
   **讲解：**  
   A. `${参数:-word}`：正确。当参数未设置或为空时，使用word作为默认值，但不修改参数本身。  
   B. `${参数:=word}`：正确。当参数未设置或为空时，将word赋值给参数，然后返回word。  
   C. `${参数:?word}`：正确。当参数未设置或为空时，输出错误信息word并退出Shell脚本。  
   D. `${参数:+word}`：正确。当参数已设置且非空时，使用word替换整个表达式，否则返回空值。  
<br>
<br>
<br>
<br>
<br>
<br>
4. 使用RPM可以很容易地对RPM软件包进行哪些操作？（ ）  
   A. 安装  
   B. 升级  
   C. 卸载  
   D. 校验  
   E. 查询  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A, B, C, D, E**  
   **讲解：**  
   A. 安装：正确。`rpm -ivh 包名.rpm`可安装指定的RPM软件包。  
   B. 升级：正确。`rpm -Uvh 包名.rpm`可升级已安装的软件包（若未安装则执行安装）。  
   C. 卸载：正确。`rpm -e 包名`可卸载已安装的指定软件包。  
   D. 校验：正确。`rpm -V 包名`可校验已安装包的文件完整性和属性是否被修改。  
   E. 查询：正确。`rpm -q 包名`可查询软件包是否已安装及版本信息。  
<br>
<br>
<br>
<br>
<br>
<br>
5. 以下哪些是Linux支持的硬件平台？（ ）  
   A. x86  
   B. x64 (AMD64/Intel 64)  
   C. ARM  
   D. SPARC  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A, B, C, D**  
   **讲解：**  
   A. x86：正确。Linux从最初就支持x86（32位）架构，是最早支持的平台之一。  
   B. x64：正确。Linux完整支持x64（AMD64/Intel 64）架构，是目前最主流的部署平台。  
   C. ARM：正确。Linux广泛应用于ARM架构（服务器、嵌入式设备、树莓派等）。  
   D. SPARC：正确。Linux支持SPARC架构（Sun/Oracle服务器）。此外还支持MIPS、PowerPC、RISC-V等。  
<br>
<br>
<br>
<br>
<br>
<br>
6. 以下哪些命令可以用于查看系统日志？（ ）  
   A. dmesg  
   B. journalctl  
   C. cat /var/log/messages  
   D. tail -f /var/log/syslog  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A, B, C, D**  
   **讲解：**  
   A. dmesg：正确。dmesg查看内核环形缓冲区日志，主要用于硬件驱动、设备初始化等信息。  
   B. journalctl：正确。journalctl查看systemd-journald收集的二进制日志，是RHEL 7+的默认日志系统。  
   C. cat /var/log/messages：正确。/var/log/messages是RHEL系列的通用系统日志文件。  
   D. tail -f /var/log/syslog：正确。/var/log/syslog是Debian系列系统日志文件。tail -f可实时追踪新日志输出。  
<br>
<br>
<br>
<br>
<br>
<br>
7. 关于用户自定义变量，以下说法正确的是（ ）  
   A. 定义语法：变量名=变量值  
   B. 定义时变量名前不应加符号$  
   C. 引用变量值时应在变量名前加$  
   D. 等号两边可以有空格  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A, B, C**  
   **讲解：**  
   A. 定义语法：变量名=变量值：正确。Shell变量定义直接使用等号连接变量名和值。  
   B. 定义时变量名前不应加符号$：正确。定义时直接写变量名，加$会导致语法错误。  
   C. 引用变量值时应在变量名前加$：正确。使用`$变量名`或`${变量名}`引用变量值。  
   D. 等号两边可以有空格：错误。Shell变量定义时等号两边不能有空格，否则会被解析为命令而非赋值。  
<br>
<br>
<br>
<br>
<br>
<br>
8. YUM相对于RPM命令的主要优势体现在哪些方面？（ ）  
   A. 自动解决软件包间的依赖关系  
   B. 可以从网络仓库自动下载并安装软件  
   C. 命令更简短易记  
   D. 保留一个包含所有软件包信息的数据库  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A, B**  
   **讲解：**  
   A. 自动解决依赖关系：正确。YUM/DNF自动分析并安装所有依赖包，这是相对于RPM的核心优势。  
   B. 从网络仓库下载：正确。YUM/DNF从配置的远程仓库自动下载软件包，无需手动寻找rpm文件。  
   C. 命令更简短易记：不准确。虽然YUM命令较简短，但RPM命令同样简洁，这不是YUM相对于RPM的核心优势。  
   D. 保留数据库：错误。RPM同样维护自己的数据库（/var/lib/rpm），这不是YUM独有的特性。  
<br>
<br>
<br>
<br>
<br>
<br>
9. 以下哪些是Linux常见的文件系统类型？（ ）  
   A. ext3  
   B. ext4  
   C. xfs  
   D. ntfs  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A, B, C**  
   **讲解：**  
   A. ext3：正确。ext3是Linux传统日志文件系统，支持最大2TB文件系统和16TB文件。  
   B. ext4：正确。ext4是ext3的继承者，支持更大容量（最大1EB），曾是RHEL 6的默认文件系统。  
   C. xfs：正确。xfs是高扩展性64位日志文件系统，RHEL 7+的默认文件系统，适合大文件和大容量存储。  
   D. ntfs：错误。ntfs是Windows的默认文件系统，Linux虽可读写（需ntfs-3g驱动）但非Linux原生文件系统。  
<br>
<br>
<br>
<br>
<br>
<br>
10. 以下关于预定义变量`$?`的说法，正确的是（ ）  
    A. 表示命令执行后返回的状态  
    B. 即上一个命令的返回代码  
    C. 值为0表示该命令正确执行  
    D. 任何非0值表示命令出错  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A, B, C, D**  
    **讲解：**  
    A. 表示命令执行后返回的状态：正确。`$?`保存每个命令执行后的退出状态码。  
    B. 即上一个命令的返回代码：正确。`$?`的值就是上一个刚执行完命令的返回码。  
    C. 值为0表示该命令正确执行：正确。退出码0在Shell/Unix系统中约定为"成功"。  
    D. 任何非0值表示命令出错：正确。非0值（1-255）表示不同类型的错误或被信号终止。  
<br>
<br>
<br>
<br>
<br>
<br>
三、 判断题（每题1分，共10分）  
<br>
<br>
<br>
<br>
<br>
<br>
1. Linux只能在基于Intel x86系列CPU的计算机上运行。  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：错**  
   **讲解：** Linux支持几乎所有硬件架构，包括x86/x64、ARM、SPARC、PowerPC、RISC-V、MIPS等，是跨平台性最强的操作系统之一。  
<br>
<br>
<br>
<br>
<br>
<br>
2. 使用ifup和ifdown命令可以激活和关闭网络接口，其效果与ifconfig enp8s0 up/down相同。  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：对**  
   **讲解：** ifup/ifdown启用/停用网络接口，与ifconfig eth0 up/down效果相同。区别在于ifup/ifdown会读取网络配置文件中的设置，而ifconfig直接操作接口不保存配置。  
<br>
<br>
<br>
<br>
<br>
<br>
3. 在Shell脚本中，以"#"开头的行都是注释。  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：对**  
   **讲解：** Shell脚本中"#"开头的行为注释。第一行`#!/bin/bash`中的`#!`是shebang特殊标记，告诉系统使用哪个解释器来执行脚本，严格来说可视为特殊的注释行。  
<br>
<br>
<br>
<br>
<br>
<br>
4. RPM判定一个文件是否是RPM格式，是根据文件扩展名是否为.rpm。  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：错**  
   **讲解：** RPM通过文件头（Header）中的魔数（Magic Number）和二进制结构来判断是否为RPM包格式，而非依赖文件扩展名。扩展名只是助记标识。  
<br>
<br>
<br>
<br>
<br>
<br>
5. 在RHEL 8中，使用ifconfig命令进行的网络配置是永久生效的。  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：错**  
   **讲解：** ifconfig配置的网络参数仅在当前会话生效，重启后丢失。若需永久配置，应使用nmcli命令或编辑`/etc/sysconfig/network-scripts/`下的对应配置文件。  
<br>
<br>
<br>
<br>
<br>
<br>
6. 在定义变量时，变量名前应加符号$，在引用变量的内容时则不应在变量名前加$。  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：错**  
   **讲解：** 恰好相反——定义变量时不加$（如`name="Alice"`），引用变量时才加$（如`echo $name`）。这是Shell初学者最常见的错误之一。  
<br>
<br>
<br>
<br>
<br>
<br>
7. YUM可以自动解决软件包间的依赖关系。  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：对**  
   **讲解：** YUM/DNF的核心特性就是自动解决依赖关系。当安装一个软件包时，YUM会自动查找并安装其所有依赖包，这是YUM相对于RPM命令的主要优势。  
<br>
<br>
<br>
<br>
<br>
<br>
8. 在安装Linux时，必须创建单独的/home分区。  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：错**  
   **讲解：** 创建单独的分区是可选的，并非必须。安装Linux时只需一个根分区（/）即可正常运行。但为方便管理和数据安全，推荐将/home、/var、/boot等分为独立分区。  
<br>
<br>
<br>
<br>
<br>
<br>
9. 在Shell中，使用 command1 && command2，表示只有 command1 执行成功时，才会执行 command2。  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：对**  
   **讲解：** &&是逻辑AND运算符：当前一个命令退出码为0（成功）时，继续执行后一个命令。若前一个命令失败（非0退出），则短路，后一个命令不执行。  
<br>
<br>
<br>
<br>
<br>
<br>
10. firewalld的运行时配置（Runtime）是永久生效的，系统重启后不会丢失，修改配置后不需要重新加载。  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：错**  
    **讲解：** firewalld的运行时配置重启后丢失。若要永久生效，需使用`--permanent`参数并将配置重载（`firewall-cmd --reload`）。运行时配置适合临时测试，永久配置适合生产环境。  
<br>
<br>
<br>
<br>
<br>
<br>
四、 简答题（每题5分，共25分）  
<br>
<br>
<br>
<br>
<br>
<br>
1. （软件包管理/Shell） 请说明使用RPM和YUM/DNF管理软件包的主要区别。并写出使用YUM安装一个名为nginx的软件包，然后启动并设置其开机自启的命令序列。  
   **答案与讲解：**  
   区别：RPM是低级包管理器，直接操作.rpm文件，无法自动解决依赖关系。YUM/DNF是高级包管理器，基于仓库（Repository），可自动下载并解决依赖。  
   命令序列：  
   ```bash  
   yum install nginx -y  
   systemctl start nginx  
   systemctl enable nginx  
   ```  
<br>
<br>
<br>
<br>
<br>
<br>
2. （文件系统/权限） 假设需要对一个目录 /shared/team 进行权限设置，要求：属主为manager，属组为teamgroup；teamgroup组成员拥有读写执行权限（7），目录内新建的文件继承父目录的属组；其他用户无任何权限（0）。请写出实现上述要求的完整命令。  
   **答案与讲解：**  
   ```bash  
   groupadd teamgroup  
   mkdir -p /shared/team  
   chown manager:teamgroup /shared/team  
   chmod 770 /shared/team        # 属主7+属组7+其他0  
   chmod g+s /shared/team        # 设置setgid确保新建文件继承属组  
   ```  
<br>
<br>
<br>
<br>
<br>
<br>
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
<br>
<br>
<br>
<br>
<br>
<br>
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
   -f测试是否为普通文件，-d测试是否为目录。`$1`是脚本第一个参数。注意`$1`使用双引号包裹防止文件名含空格导致错误。  
<br>
<br>
<br>
<br>
<br>
<br>
5. （综合管理） 列举查看系统当前进程和系统资源（如CPU、内存）使用情况的至少三个命令，并简述其各自的特点或使用场景。  
   **答案与讲解：**  
   - **ps aux / ps -ef**：进程快照，查看当前所有进程的静态信息（PID、CPU%、内存%、启动时间等）。  
   - **top / htop**：动态实时监控，自动刷新显示CPU和内存占用最高的进程。htop是top的增强版，支持鼠标操作和彩色显示。  
   - **free -h**：查看内存使用情况（总内存、已用、空闲、缓存/缓冲）。配上`-h`以人类可读格式显示。  
<br>
<br>
<br>
<br>
<br>
<br>
五、 计算题 (共10分)  
<br>
<br>
<br>
<br>
<br>
<br>
假设一个文件的权限用符号法表示为 rwxr-xr-x。  
<br>
<br>
<br>
<br>
<br>
<br>
1. 请写出该权限对应的八进制数字表示。 (2分)  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：755**  
   **讲解：** rwx=4+2+1=7（属主），r-x=4+0+1=5（属组），r-x=4+0+1=5（其他），故为755。  
<br>
<br>
<br>
<br>
<br>
<br>
2. 使用 chmod 的数字法，写出将文件权限修改为 rw-r--r-- 的命令。(2分)  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：** `chmod 644 文件名`  
   **讲解：** rw-=4+2+0=6，r--=4+0+0=4，r--=4+0+0=4，即644。  
<br>
<br>
<br>
<br>
<br>
<br>
3. 假设用户alice是属组staff的成员，但不是该文件的属主。该文件当前的符号法权限就是 rwxr-xr-x。  
   a) alice 能否读取该文件？为什么？(2分)  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：能。** alice是属组成员，权限r-x中第4-6位表示属组拥有读(r)和执行(x)权限。  
   b) alice 能否修改（写入）该文件？为什么？(2分)  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：不能。** 属组权限r-x中没有写(w)权限（rwx中的w仅针对属主）。  
   c) alice 能否执行该文件？为什么？(2分)  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：能。** 属组权限r-x中包含执行(x)权限，alice作为属组成员可以执行。  
<br>
<br>
<br>
<br>
<br>
<br>
六、 应用题 (共15分)  
<br>
<br>
<br>
<br>
<br>
<br>
场景：你是一名Linux系统管理员，需要为一台新部署的RHEL 8服务器配置基础服务环境。请根据要求完成以下任务：  
<br>
<br>
<br>
<br>
<br>
<br>
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
<br>
<br>
<br>
<br>
<br>
<br>
2. Web服务配置与防火墙（7分）：  
   **答案与讲解：**  
   ```bash  
   # 安装软件包  
   yum install httpd firewalld -y  
<br>
<br>
<br>
<br>
<br>
<br>
# 修改DocumentRoot：编辑/etc/httpd/conf/httpd.conf  
   # 将 DocumentRoot "/var/www/html" 改为 DocumentRoot "/var/www/webapp"  
   # 同时将对应 <Directory "/var/www/html"> 改为 <Directory "/var/www/webapp">  
<br>
<br>
<br>
<br>
<br>
<br>
# 启动并设置开机自启  
   systemctl start httpd  
   systemctl enable httpd  
<br>
<br>
<br>
<br>
<br>
<br>
# 防火墙配置  
   firewall-cmd --permanent --add-service=http  
   firewall-cmd --permanent --add-service=https  
   firewall-cmd --reload  
   ```  
   --permanent表示永久规则，reload使新规则生效。注意修改DocumentRoot后要确保SELinux上下文正确。  
<br>
<br>
<br>
<br>
<br>
<br>
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
<br>
<br>
<br>
<br>
<br>
<br>
《Linux操作系统》练习二  
<br>
<br>
<br>
<br>
<br>
<br>
一、 单项选择题（每题1分，共20分）  
<br>
<br>
<br>
<br>
<br>
<br>
1. 在Linux中，用于检查文件系统的磁盘空间占用情况的命令是？  
   A. df  
   B. du  
   C. fdisk  
   D. mount  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A**  
   **讲解：**  
   A. df：正确。df（Disk Free）显示文件系统整体的磁盘空间使用情况。  
   B. du：错误。du（Disk Usage）统计单个目录或文件占用的空间，而非显示文件系统的整体使用量。  
   C. fdisk：错误。fdisk用于管理磁盘分区表，不显示文件系统的空间使用信息。  
   D. mount：错误。mount用于挂载文件系统到指定挂载点，不检查磁盘空间。  
<br>
<br>
<br>
<br>
<br>
<br>
2. 哪个命令用于修改用户密码？  
   A. useradd  
   B. passwd  
   C. usermod  
   D. groupadd  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：B**  
   **讲解：**  
   A. useradd：错误。useradd用于创建新用户，不能修改密码。  
   B. passwd：正确。passwd用于设置或修改用户密码。root可修改任何用户密码，普通用户只能修改自己的密码。  
   C. usermod：错误。usermod修改用户的属性（家目录、UID等），不处理密码。  
   D. groupadd：错误。groupadd用于创建新用户组，与密码管理无关。  
<br>
<br>
<br>
<br>
<br>
<br>
3. Linux系统中存储用户账户信息（用户名、UID、GID等）但不包含加密密码的文件是？  
   A. /etc/passwd  
   B. /etc/shadow  
   C. /etc/group  
   D. /etc/login.defs  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A**  
   **讲解：**  
   A. /etc/passwd：正确。该文件存储用户名、UID、GID、家目录、登录Shell等信息，所有用户可读，密码字段用`x`占位。  
   B. /etc/shadow：错误。该文件存储加密密码及密码安全策略（过期时间等），仅root可读。  
   C. /etc/group：错误。该文件存储用户组信息（组名、GID、组成员列表）。  
   D. /etc/login.defs：错误。该文件是用户创建时的默认配置（如UID范围、密码有效期），非用户账户存储文件。  
<br>
<br>
<br>
<br>
<br>
<br>
4. 查看或编辑内核路由表的命令是？  
   A. ifconfig  
   B. route  
   C. ping  
   D. traceroute  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：B**  
   **讲解：**  
   A. ifconfig：错误。ifconfig配置和查看网络接口参数（IP地址、掩码等），不操作路由表。  
   B. route：正确。route命令用于查看和操作内核IP路由表。现代替代是`ip route`命令。  
   C. ping：错误。ping测试网络连通性（ICMP回显），不涉及路由表操作。  
   D. traceroute：错误。traceroute追踪数据包到达目标主机经过的路由路径，但不查看或修改路由表。  
<br>
<br>
<br>
<br>
<br>
<br>
5. RHEL 8中管理网络连接的核心服务是？  
   A. network  
   B. NetworkManager  
   C. net-tools  
   D. iproute2  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：B**  
   **讲解：**  
   A. network：错误。network是RHEL 6及之前版本的传统网络服务，在RHEL 8中已被取代。  
   B. NetworkManager：正确。NetworkManager是RHEL 8/9中默认的网络管理服务。  
   C. net-tools：错误。net-tools（ifconfig等）是网络工具集，不能作为系统服务管理网络连接。  
   D. iproute2：错误。iproute2（ip命令等）是底层工具集，不提供网络管理服务功能。  
<br>
<br>
<br>
<br>
<br>
<br>
6. 以下哪个组合键用于命令行自动补全？  
   A. Ctrl  
   B. Alt  
   C. Tab  
   D. Shift  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：C**  
   **讲解：**  
   A. Ctrl：错误。Ctrl是控制键，需与其他键组合使用（如Ctrl+C中断），本身不用于补全。  
   B. Alt：错误。Alt也是修饰键（如Alt+.获取上一条命令的最后一个参数），不用于自动补全。  
   C. Tab：正确。Tab键是Shell中最常用的自动补全键。连续按两次Tab可显示所有匹配项。  
   D. Shift：错误。Shift不参与命令补全，主要用于输入大写字母或符号。  
<br>
<br>
<br>
<br>
<br>
<br>
7. 使用crontab命令配置定时任务，希望每天凌晨2点30分执行一次脚本，正确的时间表达式是？  
   A. `30 2 * * *`  
   B. `2 30 * * *`  
   C. `30 2 1 * *`  
   D. `30 2 * * 0`  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A**  
   **讲解：**  
   A. `30 2 * * *`：正确。crontab格式为"分 时 日 月 周"，`30 2 * * *`表示每天2:30执行。  
   B. `2 30 * * *`：错误。分钟和小时位置颠倒，会变成每天2分30时执行（无效）。  
   C. `30 2 1 * *`：错误。多出的`1`在"日"字段，表示仅每月1号的2:30执行，非每天。  
   D. `30 2 * * 0`：错误。`0`在"周"字段表示周日，即仅每周日的2:30执行，非每天。  
<br>
<br>
<br>
<br>
<br>
<br>
8. 在Vim编辑器中，删除当前行并切换到输入模式应该按哪个键序列？  
   A. dd  
   B. cc  
   C. xx  
   D. dw  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：B**  
   **讲解：**  
   A. dd：错误。dd剪切（删除）当前行，但留在命令模式而非输入模式。  
   B. cc：正确。cc是Vim中"删除当前行并进入插入模式"的命令。  
   C. xx：错误。xx删除当前光标下的一个字符，不进入输入模式，且非整行删除。  
   D. dw：错误。dw（delete word）从光标处删除到单词末尾，不删除整行，也不进入输入模式。  
<br>
<br>
<br>
<br>
<br>
<br>
9. 用于查找名称为myapp的进程并终止它的命令是？  
   A. kill myapp  
   B. pkill myapp  
   C. killall myapp  
   D. 先ps aux | grep myapp找到PID，再用kill PID  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：D**  
   **讲解：**  
   A. kill myapp：错误。kill命令需要PID（数字）作为参数，不能直接使用进程名称。  
   B. pkill myapp：部分正确但不精确。pkill可用进程名终止进程，但可能误杀匹配名称的所有进程（如"myapp"也能匹配"myappd"）。  
   C. killall myapp：部分正确但同样不精确。killall按名称终止进程，也会误杀匹配的所有同名进程。  
   D. 先ps aux|grep找到PID，再用kill PID：正确。这是最精确的方法，先定位具体PID再终止。题目强调"查找并终止"需要两步操作，D完整描述了标准流程。  
<br>
<br>
<br>
<br>
<br>
<br>
10. 查看系统当前登录用户信息的命令是？  
    A. who  
    B. w  
    C. last  
    D. whoami  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A**  
    **讲解：**  
    A. who：正确。who显示当前登录到系统的用户列表（用户名、登录终端、登录时间）。  
    B. w：不正确。w显示更详细的信息（含系统负载、运行时间、用户进程），但不直接回答"当前登录用户"的简单查询需求。  
    C. last：错误。last查看历史登录记录（包含已登出的会话），非当前在线用户。  
    D. whoami：错误。whoami仅显示当前执行者自己的用户名，不列出所有登录用户。  
<br>
<br>
<br>
<br>
<br>
<br>
11. yum install命令在安装软件时，相比rpm -ivh的主要优势是？  
    A. 安装速度更快  
    B. 不需要root权限  
    C. 能自动解决软件包依赖关系  
    D. 命令更短  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：C**  
    **讲解：**  
    A. 安装速度更快：错误。YUM需下载和解析依赖，通常比直接rpm安装更慢而非更快。  
    B. 不需要root权限：错误。YUM和RPM安装软件包都需要root权限。  
    C. 能自动解决软件包依赖关系：正确。YUM自动从仓库下载所需所有依赖包，RPM安装时若依赖缺失会直接报错退出。  
    D. 命令更短：不是主要优势。虽然YUM命令(`yum install`)比RPM(`rpm -ivh 包名.rpm`)稍短，但这不是核心优势。  
<br>
<br>
<br>
<br>
<br>
<br>
12. 使用RPM查询一个已安装软件包（如vim-enhanced）所包含的所有文件列表，应使用哪个命令？  
    A. rpm -qa vim-enhanced  
    B. rpm -qi vim-enhanced  
    C. rpm -ql vim-enhanced  
    D. rpm -qf vim-enhanced  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：C**  
    **讲解：**  
    A. rpm -qa vim-enhanced：错误。`-qa`查询所有已安装的包，带包名参数时会匹配包含该名称的所有包。  
    B. rpm -qi vim-enhanced：错误。`-qi`（query info）查看包的详细信息（版本、描述、安装时间等）。  
    C. rpm -ql vim-enhanced：正确。`-ql`（query list）列出已安装包的全部文件及安装路径。  
    D. rpm -qf vim-enhanced：错误。`-qf`（query file）查询某个系统文件属于哪个RPM包。  
<br>
<br>
<br>
<br>
<br>
<br>
13. Samba服务器的主配置文件是？  
    A. /etc/samba/smb.conf  
    B. /etc/smb.conf  
    C. /etc/samba/smbd.conf  
    D. /var/lib/samba/smb.conf  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A**  
    **讲解：**  
    A. /etc/samba/smb.conf：正确。Samba主配置文件位于`/etc/samba/smb.conf`，定义了共享资源、安全选项和全局设置。  
    B. /etc/smb.conf：错误。路径错误，Samba配置位于`/etc/samba/`目录下。  
    C. /etc/samba/smbd.conf：错误。Samba服务的守护进程是smbd和nmbd，但配置文件名为smb.conf而非smbd.conf。  
    D. /var/lib/samba/smb.conf：错误。/var/lib/samba/存放运行时数据（如打印机驱动、用户数据库），非配置文件所在路径。  
<br>
<br>
<br>
<br>
<br>
<br>
14. 在Apache配置文件（httpd.conf）中，用于指定网站文档根目录的指令是？  
    A. ServerRoot  
    B. DocumentRoot  
    C. DirectoryIndex  
    D. Listen  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：B**  
    **讲解：**  
    A. ServerRoot：错误。ServerRoot指定Apache服务器安装目录（如`/etc/httpd`），非网站根目录。  
    B. DocumentRoot：正确。DocumentRoot指定Apache提供网页文件的根目录路径。  
    C. DirectoryIndex：错误。DirectoryIndex指定默认首页文件名（如index.html、index.php）。  
    D. Listen：错误。Listen指定Apache监听的IP地址和端口号（如`Listen 80`）。  
<br>
<br>
<br>
<br>
<br>
<br>
15. 在Shell脚本中，引用命令行第一个参数的变量是？  
    A. `$0`  
    B. `$1`  
    C. `$*`  
    D. `$$`  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：B**  
    **讲解：**  
    A. `$0`：错误。`$0`保存脚本名称或当前执行的进程名，而非命令行参数。  
    B. `$1`：正确。`$1`是脚本的第一个命令行参数。`$2`、`$3`等依次表示后续参数。  
    C. `$*`：错误。`$*`表示所有命令行参数（视为单个字符串），不特指第一个参数。  
    D. `$$`：错误。`$$`保存当前Shell进程的PID编号，与命令行参数无关。  
<br>
<br>
<br>
<br>
<br>
<br>
16. 使用chmod的数字法，将文件权限设置为rwxr-xr--，对应的数字是？  
    A. 755  
    B. 754  
    C. 644  
    D. 750  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：B**  
    **讲解：**  
    A. 755：错误。755=rwxr-xr-x，Other有执行权限（x），本题要求Other只读(r--)。  
    B. 754：正确。rwx=4+2+1=7（属主），r-x=4+0+1=5（属组），r--=4+0+0=4（其他），即754。  
    C. 644：错误。644=rw-r--r--，属主无执行权限，本题要求属主rwx。  
    D. 750：错误。750=rwxr-x---，Other无读权限（---），本题要求Other只读(r--)。  
<br>
<br>
<br>
<br>
<br>
<br>
17. 在RHEL 8中配置永久静态IP地址，可以使用以下哪种方法？（两种均可，但哪个是最佳实践）  
    A. 仅使用 ifconfig 命令。  
    B. 仅编辑 /etc/sysconfig/network-scripts/ifcfg-\* 文件。  
    C. 使用 nmcli 命令修改连接，并设置ipv4.method为manual。  
    D. 使用 ip addr add 命令。  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：C**  
    **讲解：**  
    A. ifconfig：错误。ifconfig的配置仅在当前会话生效，重启后丢失，非永久配置。  
    B. 编辑ifcfg-*文件：可永久生效，但不是最佳实践。手动编辑配置文件容易出错，且NetworkManager可能覆盖手动修改。  
    C. nmcli+manual：正确。nmcli是RHEL 8/9推荐的最佳实践，可持久化配置且自动化友好。  
    D. ip addr add：错误。ip addr add的配置同ifconfig一样，重启后丢失。  
<br>
<br>
<br>
<br>
<br>
<br>
18. 希望创建一个脚本，使其可以在任意路径下被执行，且能引用系统中定义的SHELL变量。最好的执行方式是？  
    A. 直接输入脚本名（前提是路径在PATH中）  
    B. bash script.sh  
    C. source script.sh  
    D. ./script.sh  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A**  
    **讲解：**  
    A. 直接输入脚本名：正确。将脚本放在PATH目录（如/usr/local/bin）后，直接输入脚本名可在任意路径执行，且自动继承当前Shell的环境变量。  
    B. bash script.sh：可以执行但需指定脚本路径，不是最方便的方式。  
    C. source script.sh：source在当前Shell中执行脚本，不会启动子Shell，但同样需要指定脚本路径。  
    D. ./script.sh：需要脚本有执行权限且在当前目录执行，不能实现"任意路径下执行"。  
<br>
<br>
<br>
<br>
<br>
<br>
19. 在Shell脚本中，判断变量VAR是否已设置且不为空的正确测试方法是？（双引号的使用很重要）  
    A. if [ $VAR != "" ]; then  
    B. if [ -n "$VAR" ]; then  
    C. if [ ! -z $VAR ]; then  
    D. if test -n $VAR; then  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：B**  
    **讲解：**  
    A. `[ $VAR != "" ]`：错误。若VAR为空，展开后为`[ != "" ]`，缺少条件比较对象导致语法错误。  
    B. `[ -n "$VAR" ]`：正确。`-n`测试字符串长度是否非零，双引号包裹变量防止变量展开为空时语法错误。  
    C. `[ ! -z $VAR ]`：错误。与A同理，缺少双引号，VAR为空时语法出错。  
    D. `test -n $VAR`：错误。同C，缺少双引号保护。  
<br>
<br>
<br>
<br>
<br>
<br>
20. 当执行一个RPM包的升级（rpm -Uvh package.rpm）时，如果该软件包之前从未安装过，此命令会如何处理？  
    A. 报错退出。  
    B. 自动转换为安装操作。  
    C. 询问用户是否要安装。  
    D. 仅升级相关的配置文件。  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：B**  
    **讲解：**  
    A. 报错退出：错误。rpm的-U（upgrade）模式在包未安装时会自动安装而非报错。  
    B. 自动转换为安装操作：正确。rpm -U具有"安装或升级"语义：已安装则升级，未安装则自动安装。`rpm -ivh`仅安装不升级。  
    C. 询问用户：错误。rpm不提供交互式询问，所有操作由命令行参数决定。  
    D. 仅升级配置文件：错误。升级操作会更新整个软件包的内容，不仅限于配置文件。  
<br>
<br>
<br>
<br>
<br>
<br>
二、 多项选择题（每题2分，共20分）  
<br>
<br>
<br>
<br>
<br>
<br>
1. 以下哪些命令属于 iproute2 新工具包，用于替代 net-tools 旧工具包？（ ）  
   A. ifconfig -> ip addr  
   B. route -> ip route  
   C. netstat -> ss  
   D. arp -> ip neigh  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A, B, C, D**  
   **讲解：** iproute2全面替代net-tools：ip addr替代ifconfig，ip route替代route，ss替代netstat，ip neigh替代arp。建议新系统使用iproute2系列命令。  
<br>
<br>
<br>
<br>
<br>
<br>
2. 关于ps命令，以下说法正确的是？（ ）  
   A. ps aux：显示所有用户的所有进程详细信息。  
   B. ps -ef：使用标准格式列出所有进程。  
   C. 显示进程的树状结构应使用pstree。  
   D. 要动态观察进程状态，使用top或htop。  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A, B, C, D**  
   **讲解：** ps aux和ps -ef是最常用的两个进程查看格式。pstree以树状展示父子关系。top/htop动态实时刷新显示。  
<br>
<br>
<br>
<br>
<br>
<br>
3. 关于find命令，以下哪些参数或操作是正确的？（ ）  
   A. -name：按文件名查找。  
   B. -type：按文件类型（如f、d）查找。  
   C. -exec：对查找到的文件执行后续命令。  
   D. -size：按文件大小查找。  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A, B, C, D**  
   **讲解：** find常用参数：-name按名称、-type按类型（f文件/d目录/l链接）、-exec执行命令、-size按大小（如+100M）。多个条件可组合使用。  
<br>
<br>
<br>
<br>
<br>
<br>
4. 用户自定义变量赋值时，正确的规则是？（ ）  
   A. 变量名=值（等号两边不能有空格）。  
   B. 值中有空格，需要用引号引起来。  
   C. 引用变量值时，应在变量名前加$。  
   D. 定义时，应在变量名前加$。  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A, B, C**  
   **讲解：** Shell变量定义语法：`变量名=值`，等号两侧不能有空格。值含空格需引号。定义不加$，引用加$。D恰好说反。  
<br>
<br>
<br>
<br>
<br>
<br>
5. 以下哪些是关于YUM仓库配置的正确描述？（ ）  
   A. 配置文件存储在 /etc/yum.repos.d/ 目录下，后缀为 .repo。  
   B. enabled=1 表示启用此仓库。  
   C. gpgcheck=1 表示启用GPG签名检查。  
   D. baseurl 指定了仓库的URL地址，可支持 http://, ftp://, file://。  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A, B, C, D**  
   **讲解：** YUM仓库配置的四个核心字段：baseurl指定URL、enabled控制启用、gpgcheck控制签名检查、name指定名称。配置文件以.repo结尾放在/etc/yum.repos.d/下。  
<br>
<br>
<br>
<br>
<br>
<br>
6. SELinux的主要运行模式有哪几种？（ ）  
   A. Enforcing：强制执行安全策略。  
   B. Permissive：记录违规但不阻止。  
   C. Disabled：完全禁用。  
   D. Audit：仅进行审计。  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A, B, C**  
   **讲解：**  
   A. Enforcing：正确。Enforcing模式强制执行SELinux安全策略，违规操作被阻止并记录日志。  
   B. Permissive：正确。Permissive模式仅记录违规行为但不阻止，常用于调试和策略开发。  
   C. Disabled：正确。Disabled模式完全禁用SELinux，不加载任何策略。  
   D. Audit：错误。"Audit"不是SELinux的独立运行模式。Permissive模式下就会执行审计记录。  
<br>
<br>
<br>
<br>
<br>
<br>
7. Apache HTTP 服务器中可以实现基于以下哪些方式的虚拟主机？（ ）  
   A. 基于IP地址。  
   B. 基于端口号。  
   C. 基于主机名（域名）。  
   D. 基于用户认证。  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A, B, C**  
   **讲解：**  
   A. 基于IP地址：正确。Apache可为不同IP地址配置独立的虚拟主机站点。  
   B. 基于端口号：正确。Apache可在不同端口上运行不同站点（如80和8080）。  
   C. 基于主机名/域名：正确。基于名称的虚拟主机最常用，多个域名共享同一IP地址，通过HTTP Host头区分。  
   D. 基于用户认证：错误。用户认证是Apache的访问控制功能，并非虚拟主机类型。  
<br>
<br>
<br>
<br>
<br>
<br>
8. DHCP客户端与服务器交互获取IP地址的过程，正确的报文顺序是？（ ）  
   A. DISCOVER, OFFER, REQUEST, ACK  
   B. OFFER, DISCOVER, REQUEST, ACK  
   C. DISCOVER, REQUEST, OFFER, ACK  
   D. 首先由客户端广播DHCPDISCOVER。  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A, D**  
   **讲解：**  
   A. DISCOVER→OFFER→REQUEST→ACK：正确。这是DHCP标准的DORA交互过程。  
   B. OFFER→DISCOVER→...：错误。OFFER必须在DISCOVER之后，因为服务器必须先收到客户端的发现请求才能响应。  
   C. DISCOVER→REQUEST→OFFER→...：错误。REQUEST应在收到OFFER之后发送，顺序应为D→O→R→A。  
   D. 首先由客户端广播DHCPDISCOVER：正确。D描述了DORA的第一步，也是正确的描述。  
<br>
<br>
<br>
<br>
<br>
<br>
9. 关于Firewalld和IPTables的关系，以下说法正确的是？（ ）  
   A. Firewalld是RHEL 7/8中的默认防火墙管理工具。  
   B. Firewalld可以提供动态配置，无需重启服务即可应用新规则。  
   C. Firewalld默认后端在RHEL 8中是nftables，但RHEL 7可能使用iptables。  
   D. iptables命令已完全被淘汰，无法在RHEL 8中使用。  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A, B, C**  
   **讲解：**  
   A. Firewalld是RHEL 7/8默认防火墙：正确。Firewalld是RHEL 7及以上版本的默认防火墙管理工具。  
   B. Firewalld支持动态配置：正确。Firewalld可运行时动态增减规则，无需重启服务。  
   C. RHEL 8后端为nftables，RHEL 7可能用iptables：正确。RHEL 8的Firewalld默认使用nftables后端，RHEL 7可使用iptables后端。  
   D. iptables已完全淘汰：错误。iptables命令在RHEL 8中仍可使用（通过nftables兼容层），未完全淘汰。  
<br>
<br>
<br>
<br>
<br>
<br>
10. 关于网络地址转换（NAT）与防火墙，以下说法正确的是？（ ）  
    A. iptables 的 nat 表用于实现网络地址转换。  
    B. firewalld 是 RHEL 7/8 中默认的防火墙管理工具。  
    C. firewalld 的后端可以是 iptables 或 nftables。  
    D. 防火墙规则不仅可以过滤数据包，还可以修改数据包的地址（NAT）。  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：A, B, C, D**  
    **讲解：**  
    A. iptables的nat表用于NAT：正确。iptables的nat表专门用于源地址转换（SNAT）和目标地址转换（DNAT）。  
    B. Firewalld是RHEL 7/8默认防火墙：正确。Firewalld是RHEL 7及以上版本的默认防火墙管理工具。  
    C. Firewalld后端可为iptables或nftables：正确。Firewalld支持选择iptables或nftables作为后端执行引擎。  
    D. 防火墙可做NAT地址修改：正确。防火墙不仅过滤数据包，还可通过NAT修改数据包的源地址或目标地址。  
<br>
<br>
<br>
<br>
<br>
<br>
三、 判断题（每题1分，共10分）  
<br>
<br>
<br>
<br>
<br>
<br>
1. rmdir命令可以删除非空目录。  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：错**  
   **讲解：** rmdir只能删除空目录。删除非空目录应使用`rm -rf`命令，但需谨慎使用。  
<br>
<br>
<br>
<br>
<br>
<br>
2. du命令用于统计文件和目录占用的磁盘空间大小。  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：对**  
   **讲解：** du（Disk Usage）统计文件/目录的磁盘使用量。`du -sh`查看目录总大小，`du -h`以人类可读格式显示。  
<br>
<br>
<br>
<br>
<br>
<br>
3. 在Shell中，`$?`变量的值表示上一个命令的退出状态，0通常代表成功。  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：对**  
   **讲解：** `$?`是Shell预定义变量，保存上一个命令的退出码。0表示成功，非0表示不同类型的错误或信号终止。  
<br>
<br>
<br>
<br>
<br>
<br>
4. 使用rpm -e卸载软件包时，如果存在依赖关系，会强制卸载。  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：错**  
   **讲解：** 默认情况下rpm -e会检查依赖关系，若其他已安装包依赖该包则拒绝卸载并报错退出。`--nodeps`参数可跳过依赖检查，但可能导致其他软件包无法正常运行。  
<br>
<br>
<br>
<br>
<br>
<br>
5. Samba服务器使用139和445端口进行文件共享通信。  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：对**  
   **讲解：** Samba使用TCP 139（NetBIOS会话服务）和445（SMB over TCP）端口提供文件共享和打印服务。  
<br>
<br>
<br>
<br>
<br>
<br>
6. TCP Wrappers通过检查/etc/hosts.allow和/etc/hosts.deny来实现访问控制，检查顺序是先hosts.deny，再hosts.allow。  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：错**  
   **讲解：** TCP Wrappers检查顺序是hosts.allow优先于hosts.deny。先在allow中查找，匹配则放行；不匹配再查deny，匹配则拒绝。allow的优先级高于deny。  
<br>
<br>
<br>
<br>
<br>
<br>
7. 在使用Vim编辑器时，输入:wq可以保存文件并退出。  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：对**  
   **讲解：** :wq在Vim末行模式中表示"写入（write）并退出（quit）"。等效命令还有`:x`和命令模式下的`ZZ`。  
<br>
<br>
<br>
<br>
<br>
<br>
8. systemctl status firewalld命令用于查看firewalld服务的当前状态。  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：对**  
   **讲解：** systemctl status显示服务的运行状态、PID、最近日志等信息。加`-l`参数可显示完整输出。  
<br>
<br>
<br>
<br>
<br>
<br>
9. 使用useradd命令时，如果不加-m参数，默认不会创建用户的主目录。  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：错**  
   **讲解：** 在RHEL中，useradd命令默认创建家目录，即使不加-m参数。`/etc/login.defs`文件中的`CREATE_HOME`配置控制此行为，RHEL默认为yes。因此"不加-m就不创建"的说法是错误的。添加-m参数显式要求创建家目录。  
<br>
<br>
<br>
<br>
<br>
<br>
10. 在Linux权限管理中，为目录设置setgid位（如chmod g+s dir）后，任何用户在此目录下创建的新文件，其属组都会继承该目录的属组。  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：对**  
    **讲解：** setgid（SGID）位的作用：对目录设置后，在该目录下创建的文件/子目录自动继承目录的属组而非创建者的主要组。可用`chmod g+s`或数字法以`2`为前缀（如2755）设置。  
<br>
<br>
<br>
<br>
<br>
<br>
四、 简答题（每题5分，共25分）  
<br>
<br>
<br>
<br>
<br>
<br>
1. （系统管理） 简述/etc/passwd文件和/etc/shadow文件各自的用途及区别。  
   **答案与讲解：**  
   用途：  
   - /etc/passwd：存储用户账户基本信息（用户名、UID、GID、家目录、登录Shell），所有用户可读。  
   - /etc/shadow：存储加密密码及安全策略（密码过期天数、失效时间等），仅root可读。  
   区别：/etc/passwd的密码字段在现代Linux中为`x`或`*`占位，实际加密密码移至/etc/shadow。这种分离增强了安全性。  
<br>
<br>
<br>
<br>
<br>
<br>
2. （系统命令） 请分别写出完成以下功能的命令：  
   a) 查看/var/log/messages文件的最新10行内容，并持续监控其新增内容。  
   b) 查找系统中所有在过去24小时内修改过的普通文件，并以列表形式输出详细信息。  
   **答案与讲解：**  
   a) `tail -f /var/log/messages` 或 `tail -10f /var/log/messages`。`-f`（follow）持续追踪文件新增内容。  
   b) `find / -type f -mtime -1 -ls`。`-type f`普通文件，`-mtime -1`表示1天内（24小时内）修改过，`-ls`以列表格式输出。  
<br>
<br>
<br>
<br>
<br>
<br>
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
<br>
<br>
<br>
<br>
<br>
<br>
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
<br>
<br>
<br>
<br>
<br>
<br>
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
<br>
<br>
<br>
<br>
<br>
<br>
五、 计算题 (共10分)  
<br>
<br>
<br>
<br>
<br>
<br>
有一个新建项目团队teamA，项目目录为/data/projectA。团队成员user1和user2都需要能读写目录内的文件并创建新文件。同时，user1是项目负责人，需要能够设置文件权限。  
<br>
<br>
<br>
<br>
<br>
<br>
1. 创建组teamA，并将user1和user2添加至teamA组的命令。（2分）  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：**  
   ```bash  
   groupadd teamA  
   usermod -aG teamA user1  
   usermod -aG teamA user2  
   ```  
   **讲解：** -aG是追加到补充组（不会移除已有组）。注意：不加-G参数会修改用户主要组而非追加。  
<br>
<br>
<br>
<br>
<br>
<br>
2. 创建/data/projectA目录，设置其属组为teamA，权限为775（rwxrwxr-x）。写出命令。（2分）  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：**  
   ```bash  
   mkdir -p /data/projectA  
   chown :teamA /data/projectA  
   chmod 775 /data/projectA  
   ```  
   **讲解：** `chown :teamA`只改属组不改属主（省略属主部分），等效于`chgrp teamA`。  
<br>
<br>
<br>
<br>
<br>
<br>
3. 希望保证无论谁在/data/projectA目录下新建文件，其所属组都是teamA。需要额外添加一个特殊权限位。请写出对应的chmod命令（使用数字法）。（2分）  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：** `chmod 2775 /data/projectA`  
   **讲解：** 数字法中的前缀`2`表示设置setgid位。2755=setgid(2)+rwx(7)+rwx(7)+r-x(5)。之后该目录下新建的文件自动继承teamA属组。  
<br>
<br>
<br>
<br>
<br>
<br>
4. user1创建了脚本script.sh（初始权限644）。为了让teamA组成员都能执行此脚本，修改权限的命令（字母法和数字法各写一次）。（2分）  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：**  
   ```bash  
   # 字母法  
   chmod g+x /data/projectA/script.sh  
   # 数字法  
   chmod 774 /data/projectA/script.sh  
   ```  
   **讲解：** 644=rw-r--r--，给组加执行权限后变为rwxrw-r--（数字法计算：6+0=6不变，4+1=5变7，故为774）。  
<br>
<br>
<br>
<br>
<br>
<br>
5. 将/data/projectA目录权限从777改为撤销其他用户（Other）的写（w）和执行（x）权限后的值（数字法），并写出chmod命令。（2分）  
<br>
<br>
<br>
<br>
<br>
<br>
**答案：** `chmod 755 /data/projectA`  
   **讲解：** 777=rwxrwxrwx，撤销Other的wx（去掉2+1=3）后，Other部分由7变为5(r-x)，即755。其他用户仅保留读和执行权限。  
<br>
<br>
<br>
<br>
<br>
<br>
六、 应用题 (共15分)  
<br>
<br>
<br>
<br>
<br>
<br>
你是某公司的Linux系统管理员，需要为一台新的应用服务器（RHEL 8）配置基础环境，确保其上运行的Web应用安全稳定。请完成以下任务：  
<br>
<br>
<br>
<br>
<br>
<br>
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
<br>
<br>
<br>
<br>
<br>
<br>
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
<br>
<br>
<br>
<br>
<br>
<br>
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