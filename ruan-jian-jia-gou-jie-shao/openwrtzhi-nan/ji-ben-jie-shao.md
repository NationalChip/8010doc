# 基本介绍 {#基本介绍}

我们现在的整个应用SDK是基于openwrt进行编译的。

通过openwrt我们可以得到文件系统，交叉编译工具链等。

---

**嵌入式系统模块**

RBL – ROM Boot：

ROM引导加载程序，芯片厂商固化在芯片内部的程序，用于引导SPL

SPL – Secondary Program Loader：

二级程序加载器，主要负责加载U-boot代码到内存中运行

U-boot：

BootLoader，主要负责加载内核镜像到DDR，并跳转到内核运行

kernel：

操作系统核心，提供操作系统所需的核心功能：进程管理、内存管理、文件系统、网络、安全机制、设备驱动等

rootfs：

根文件系统

---

**Linux启动的4个步骤**

1. ROM 从外部存储器中读取SPL到内部RAM

2. SPL进行部分初始化设置，并从外部存储器中读取u-boot到DDR中执行

3. u-boot做进一步初始化设置，从外部存储器中读取内核镜像到DDR中执行

4. Linux启动并初始化用户运行时环境

---

**网络配置**

使用文件系统，进行NFS调试的时候，容易出现：nfs: server 192.168.1.174 not responding, still trying

这样的错误

解决办法：

编辑根目录下/etc/config/network，将以下参数屏蔽：

\#config interface 'lan'

\# \#option type 'bridge'

\# option ifname 'eth0'

\# option proto 'static'

\# option ipaddr '192.168.111.136'

\# option netmask '255.255.254.0'

\# option ip6assign '60'+

---

**启动流程**

关注 /etc/rc.d 目录内的内容

关注 /usr/bin/startapp的内容

