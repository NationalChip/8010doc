# gx8010\_SSD开发板环境搭建指南

**主机系统环境搭建**

推荐使用ubuntu 16.04 64

下载地址： 链接：[https://pan.baidu.com/s/11s9t05d7n-FLAafghiPqPQ](https://pan.baidu.com/s/11s9t05d7n-FLAafghiPqPQ)密码：5mfj

面的例子是在win7 64位系统加VMWare ubuntu 16.04 64位上进行演示

使用的是linux虚拟机

  
**OPENWRT编译**

下载nationalchip的5个工程：openwrt, senseflow, kernel, uboot, mpd，放在同一目录下

注意，名字确保为：openwrt, senseflow, kernel, uboot, mpd

  
安装编译依赖工具：

Ubuntu16.04:

sudo apt-get install build-essential subversion libncurses5-dev zlib1g-dev gawk gcc-multilib flex git-core gettext libssl-dev unzip texinfo device-tree-compiler

进入openwrt目录：

cd openwrt

拷贝默认方案配置到.config，例如选用smart speaker demo方案：

cp configs/ leo\_gx8010\_ssd\_1v\_defconfig .config

make defconfig

make V=s -j4

如果编译错误，请用 make V=s 编译进行查看

请尽量使用我们推荐的linux镜像，否则容易出现编译异常

  
编译生成的固件存放在bin目录下，包括mcu, uboot，kernel，dtb，rootfs，ubifs，tf卡镜像等

下图是固件的一些简单说明

![](/assets/firms.png)

如果需要修改openwrt package配置：

make menuconfig

修改kernel配置：

make kernel\_menuconfig

编译单个package：

make package/some\_package/clean

make package/some\_package/compile V=s

  
**镜像烧录**

参考：[https://legacy.gitbook.com/book/13421398942/gx8010-gx8008](https://legacy.gitbook.com/book/13421398942/gx8010-gx8008)

  
**分区说明**

![](/assets/spi_nand.png)![](/assets/spi_nor.png)

  
**FTP加NFS方式调试**

FTP安装

Linux下安装：

搭建tftp服务器：

安装服务：

sudo apt-get install tftpd-hpa

配置：

编辑/etc/default/tftpd-hpa

TFTP\_USERNAME="tftp"

TFTP\_DIRECTORY="/opt/tftpboot"

TFTP\_ADDRESS="0.0.0.0:69"

TFTP\_OPTIONS="--secure"

TFTP\_OPTIONS="-l -c -s"

可根据需求修改tftp目录 TFTP\_DIRECTORY

重启tftp服务：

sudo service tftpd-hpa restart

如果遇到连接不上的问题，可能是防护墙导致的

Linux上执行：sudo ufw allow from &lt;开发板ip&gt;

Windows上，把本地防护墙关闭

  
Linux 环境下搭建 NFS 服务器

搭建nfs服务器：

安装服务：

sudo apt-get install nfs-kernel-server

配置：

编辑/etc/exports

/opt 192.168.\*\(rw,sync,crossmnt,no\_subtree\_check,no\_root\_squash\)

以上配置使/opt目录及子目录可被192.168.\*网段的nfs客户端访问，可根据实际情况配置

重启nfs服务：

sudo service nfs-kernel-server restart

第一步：开发板串口接上CK uart端和PC端。开发板上电，这个时候 win7 系统会要求我们安装usb串口驱动，使用网盘提供的 “CH341SER-开发板串口驱动.zip” 进行安装

地址：

第二步：使用PC端的串口工具，比如 SecureCRT，发板串口接上CK uart端和PC端，开发板上电，这个时候SecureCRT 会有串口输出 。串口参数为115200 ，8N1

第三步：切入到 ubuntu 虚拟机，设置虚拟机，识别到 usb 串口

![](/assets/vmware.png)

第四步：在 linux 下，通过mcu端的串口来烧写mcu.bin，u-boot.img，u-boot-spl.bin

注意，在板子上插入 ck-uart这个接口

烧录方式参考：[https://legacy.gitbook.com/book/13421398942/gx8010-gx8008](https://legacy.gitbook.com/book/13421398942/gx8010-gx8008)

开发板串口接上ARM uart端和PC端，开发板接上网线，然后开发板上电，这个时候会通过串口工具看到uboot的打印，回车，进入uboot的命令模式

![](/assets/uboot.png)

这个时候，通过 uboot 进行开发板 TFTP 下载的网络设置

命令如下：

setenv serverip 192.168.1.188 ------ 设置服务器

setenv ipaddr 192.168.1.211 -------本机设置

saveenv -------配置保存

通过TFTP加载linux 内核, 命令如下：

run netload

注意：这个命令是把linux 内核加载到指定位置的内存上，但是并不会运行

下载如果报名字不对找不到文件的话，自己修改下文件名就可以了

![](/assets/netload.png)

可以看到下载过程：

![](/assets/downloading.png)

下载完毕后，显示如下：

![](/assets/downloaded.png)

第六步：通过NFS 加载 root fs 根文件系统

将root-brcm2708.tar.gz （根据自己编译的openwrt实际情况来）解压到 ”/opt/rootfs”目录下，并拷贝/sbin目录下的init文件到文件系统根目录下（否则会报init错误，文件系统起不来）

在开发板上通过 uboot 命令进行 NFS 启动的设置：

1

setenv nfsboot "setenv bootargs console=ttyS0,115200 init=/init root=/dev/nfs rw nfsroot=192.168.1.134:/opt/rootfs/aliyun\_rootfs ip=192.168.1.211;bootz ${loadaddr} - ${fdt\_addr}"

2

saveenv

然后开发板上执行：run nfsboot

就可以看到 linux kernel启动，并加载 rootfs ，最后启动 demo 程序

会出现NFS的超时问题，修改：

/etc/config/network

把配网相关的屏蔽掉

如下：

  
config interface 'loopback'

option ifname 'lo'

option proto 'static'

option ipaddr '127.0.0.1'

option netmask '255.0.0.0'

config globals 'globals'

option ula\_prefix 'fdd9:6a4a:6144::/48'

\#config interface 'lan'

\#option ifname 'eth0'

\#option proto 'static'

\#option ipaddr '192.168.1.1'

\#option netmask '255.255.255.0'

\#option ip6assign '60'

  
**其它**

为了避免驱动调试信息影响，可以关闭内核级打印

echo 4 &gt;/proc/sys/kernel/printk

  
关闭senseflow应用

使用：killall senseflow

或者在/usr/bin/startapp中修改

