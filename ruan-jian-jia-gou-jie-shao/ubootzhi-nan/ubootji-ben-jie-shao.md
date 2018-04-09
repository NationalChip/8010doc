# UBOOT基本介绍 {#uboot基本介绍}

uboot是bootLoader的一种， 运行在内核启动前的一段裸机代码，主要是为启动内涵准备环境，并加载内核启动它。

---

**uboot目录结构**![](/assets/uboot_dir.png)├──api 存放uboot提供的接口函数

├──arch 与体系结构相关的代码

├──board 板级相关代码

├──cmd uboot命令代码目录

├──common 通用代码

├──configs 配置文件,make xxx\_defconfig就是放在这个目录下

├──disk 磁盘相关代码

├──doc uboot相关的说明文档

├──drivers 各种硬件驱动

├──dts 存放的是设备树的Kconfig和Makefile

├──examples 示例代码

├──fs 文件系统相关

├──include 各种共用头文件

├──lib 通用库的源码文件

├──Licenses 开源许可相关

├──net 网络相关

├──post 上电自检测的代码

├──scripts 一些辅助脚本

├──test 测试代码

├──tools uboot的各种工具软件

![](/assets/uboot_struct.gif)



