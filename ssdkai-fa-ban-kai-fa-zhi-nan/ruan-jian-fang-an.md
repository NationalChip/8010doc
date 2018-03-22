# **软件方案**

## **概述**

GX8010 SSD Kit SDK 是完整的智能音箱离线方案，包括唤醒识别、语音识别（ASR）、自然语言处理（NLP）、返回意图识别结果（可定制skill）等，打通从底层到应用的整条链路。启动SSD 平台，运行demo 程序，可进行基本的本地语音交互。SDK 主要模块包含：

* OpenWRT：提供完整的编译环境，进行Toolchain 下载，并编译Uboot、Kernel、系统软件和应用软件，生成需要的全部固件和根文件系统。

* Senseflow：信号处理系统，负责语音拾取、前处理和特征提取，能调用语言模型处理数据、合成语音和分词，可播放语音，并与云端交互。

* Mpd：语音播放器，运行在单独的进程中，接收Senseflow 的控制指令实现音频流的播放。

* Uboot：为内核启动准备环境，并加载内核。

* Kernel：提供Linux 操作系统。

当前离线方案支持以下功能：

1. 通过”萝卜头”唤醒词触发唤醒状态

2. 通过按键触发唤醒状态

3. 支持音乐播放功能

4. 支持TTS 播放功能

5. 本地技能平台，可自定义简单的skill

---

## **方案设计简介**

智能音箱方案使用OpenWRT 来编译所有模块，生成所需要的固件和根文件系统，OpenWRT 整体软件框架如下图：

![](/assets/OpenWRT 软件框架.png)

```
                               OpenWRT 软件框架
```

其中第一行为原始目录，第二行为编译过程中生成的目录，我们将Senseflow、Mpd 和Uboot 设计为package 下的软件包，通过Makefile 来定义软件包版本、下载地址、编译方式和安装地址等。在target 里定义firmware 和Kernel 的编译过程。编译完成后firmware和ipk都会放在bin 目录对应的板级内。

#### FLASH 分区

GX8010 SSD 平台使用NOR Flash 时，文件系统存放在SD 卡的mmcblock2 上，若使用NAND Flash，则需要划出文件系统分区\(灰色部分\)，具体分区情况如下：

| 分区名 | 起始地址 | 结束地址 | 分区大小 |
| :---: | :---: | :---: | :---: |
| mcu | 0x000000 | 0x100000 | 1M |
| uboot\_stage1 | 0x100000 | 0x140000 | 256k |
| uboot\_env | 0x140000 | 0x180000 | 256k |
| uboot\_stage2 | 0x180000 | 0x200000 | 512k |
| boot\_music | 0x200000 | 0x280000 | 512k |
| kernel\_dtb | 0x280000 | 0x300000 | 512k |
| kernel\(nor\) | 0x300000 | 0x800000 | 5M |
| kernel\(nand\) | 0x300000 | 0xa00000 | 7M |
| rootfs\(nand\) | 0xa00000 | 0x8000000 | 118M |

```
                             flash 分区
```

![](/assets/flash 布局和启动流程.png)

```
                          flash 布局和启动流程
```

---

## **SDK 获取流程**

GX8010 SSD Kit SDK 开发套件包含以下软件组件：

* openwrt-leo-gx8010-ssd-1v.dtb

* openwrt-leo-gx8010-ssd-1v-zImage

* openwrt-leo-gx8010-ssd-1v-u-boot.img

* openwrt-leo-gx8010-ssd-1v-u-boot-spl.bin

* openwrt-leo-gx8010-ssd-1v-ubi.img

* openwrt-leo-gx8010-ssd-1v-sdcard-vfat-ext4.img

* openwrt-leo-gx8010-ssd-1v-rootfs.tar.gz

* toolchain-arm\_cortex-a7+neon-vfpv4\_gcc-5.3.0\_glibc-2.22\_eabi.tar.gz

* toolchain-arm\_cortex-a7 neon-vfpv4\_gcc-5.3.0\_musl-1.1.16\_eabi.tar

* mcu.bin

* leo\_nre.boot

* bootx

以上开发套件包含了系统设备树、Linux 内核、Bootloader、根文件系统、OpenWRT 交叉编译工具链、MCU 固件和串口烧录工具。

---

## **开发环境搭建**

推荐使用 ubuntu 16.04 64 位或者 ubuntu 14.04 64 位。

#### 工具链安装

安装OpenWRT 编译工具链，对应在SDK 中名称为：

toolchain-arm\_cortex-a7+neon-vfpv4\_gcc-5.3.0\_glibc-2.22\_eabi.tar.gz

toolchain-arm\_cortex-a7 neon-vfpv4\_gcc-5.3.0\_musl-1.1.16\_eabi.tar

通过tar zxvf 命令解压后，放到某个目录下，如/opt/

然后在用户的~/.bashrc 文件内添加该路径到path 变量：

export

PATH="$PATH:/opt/toolchain-arm\_cortex-a7+neon-vfpv4\_gcc-5.3.

0\_musl-1.1.16\_eabi/bin"

#### 搭建TFTP 服务器

1.安装服务：sudo apt-get install tftpd-hpa

2.配置：编辑/etc/default/tftpd-hpa 文件

TFTP\_USERNAME="tftp"

TFTP\_DIRECTORY="/opt/tftpboot"

TFTP\_ADDRESS="0.0.0.0:69"

TFTP\_OPTIONS="--secure"

TFTP\_OPTIONS="-l -c -s"

可根据需求修改tftp 目录 TFTP\_DIRECTORY

3.重启tftp 服务：

sudo service tftpd-hpa restart

#### 搭建NFS 服务器

1.安装服务：sudo apt-get install nfs-kernel-server

2.配置：编辑/etc/exports 文件

/opt

192.168.\*\(rw,sync,crossmnt,no\_subtree\_check,no\_root\_squash\)

以上配置使/opt 目录及子目录可被192.168.\*网段的nfs 客户端访问，可根据实际情  
况配置。

3.重启nfs 服务：sudo service nfs-kernel-server restart

---

## **固件烧写**

通过MCU 端串口来进行固件的烧写， 烧写的工具为bootx ，使用leo\_nre.boot\(进行烧录的片上运行程序\)，烧写命令在Linux shell 下执行，每次烧写都需要板子reset 或重新上电，具体的烧写命令如下：

1.download mcu.bin

./bootx -b ./leo\_nre.boot -d /dev/ttyUSB0 -c serialdown 0

mcu.bin

2.download uboot stage1

./bootx -b ./leo\_nre.boot -d /dev/ttyUSB0 -c serialdown 0x100000

openwrt-leo-gx8010-ssd-1v-u-boot-spl.bin

3.download uboot stage2

./bootx -b ./leo\_nre.boot -d /dev/ttyUSB0 -c serialdown 0x180000

openwrt-leo-gx8010-ssd-1v-u-boot.img

Note：/dev/ttyUSB0 需要修改为本机对应的MCU 端串口号。

成功烧写后，重新启动系统，能在MCU 端串口看到MCU 的启动信息，在CPU串口看到uboot 启动信息，接入喇叭的情况下能够听到“系统启动中”提示音。

---

## TFTP Boot 启动

启动过程可分为3 个主要部分：Bootloader，Kernel，rootfs，其中Kernel和rootfs 均可选择不同的加载方式，通过CPU 端的串口登入uboot 环境来配置启动方式。下面介绍通过tftp 服务将内核镜像zImage 下载到开发板的DDR 空间进行引导启动Linux 内核。

1.将openwrt-leo-gx8010-ssd-1v-zImag 和openwrt-leo-gx8010-ssd-1v.dtb 放到主机的tftp 目录下，如/opt/tftpboot/。

2.PC 打开CPU 端串口终端，开发板接上12V 电源，按下按键进行上电，此时串口终端显示如下界面，则代表启动成功。![](/assets/串口启动界面.png)

```
                                                          串口启动界面
```

3.回车，进入uboot 的命令模式，根据实际环境设置主机ip 地址serverip和开发板ip 地址ipaddr：

setenv serverip 192.168.xxx.xxx

setenv ipaddr 192.168.xxx.yyy

Note：192.168.xxx.xxx 和192.168.xxx.yyy 必须在同一个网段。

配置烧写命令：

setenv burn "sf probe;sf erase 0x400000 0x400000;sf write

0x10008000 0x400000 0x3f0000"

保存：saveenv

4.将Kernel zImage 下载到本地：run netload，烧写至flash：run burn。

根据板载flash 类型的不同，burn 命令也有所不同，默认情况下已配好，下面列出其值作为参考。

下面列出其值作为参考。

* NOR Flash：run norburn

norburn=run netload;sf probe;sf erase ${fdt\_offs} 0x580000;sf

write ${kernel\_addr} ${kernel\_offs} ${kernel\_size};sf write

${fdt\_addr} ${fdt\_offs} ${fdt\_size};

* NAND Flash：run nandburn

nandburn=run netload;snd i;snd e ${fdt\_offs} 0x780000;snd w

${fdt\_offs} ${fdt\_addr} ${fdt\_size};snd w ${kernel\_offs}

${kernel\_addr} ${kernel\_size}

烧写完成后便能够加载Kernel，加载方式也与flash 介质有关

* NOR Flash：run norload

norload=sf probe；sf read ${fdt\_addr} ${fdt\_offs} ${fdt\_size};

sf read ${kernel\_addr} ${kernel\_offs} ${kernel\_size}

* NAND Flash：run nandload

nandload=snd i; snd r ${fdt\_offs} ${fdt\_addr} ${fdt\_size};

snd r ${kernel\_offs} ${kernel\_addr} ${kernel\_size}

Note：上述命令只能够加载Kernel，但并未启动系统，还需挂载rootfs。

变量中各个参数的值如下：

* kernel\_addr=0x10008000 kernel\_offs=0x300000 kernel\_size=0x500000

* fdt\_addr=0x10000100 fdt\_offs=0x280000 fdt\_size=0x8000

Note：若修改了参数值，或参数与上述不符，则可在uboot 命令行输入：

sf probe;sf erase 0x140000 0x10000

擦除env 区域，断电重启后即可加载默认配置。

也可使用下面命令加载默认参数：

env default -a -f;saveenv

---

## Rootfs 加载方式

#### SPI + SD 卡

从SD 卡加载rootfs 分为以下步骤

1.首先需要在主机上将包含rootfs 的镜像文件烧到SD 卡中，可使用df -h 查看SD 卡挂载，然后使用dd 命令进行烧录：

sudo dd if=openwrt-leo-gx8010-ssd-1v-sdcard-vfat-ext4.img

of=/dev/sdb

Note：/dev/sdb 是SD 卡的设备节点，实际情况可能有所不同，请务必确认正确的设备名。完成后，请记得执行sync 命令，避免数据没有完全写入到 SD 卡的问题。

2.配置启动参数（默认已配好）

mmcboot=setenv bootargs console=ttyS0,115200 root=${mmcroot}

rw rootwait;bootz ${kernel\_addr} - ${fdt\_addr}

mmcroot=/dev/mmcblk0p2

3.将烧好rootfs 镜像的SD 卡插入开发板卡槽，加载内核之后，在uboot 环境执行run

mmcboot 启动系统并从SD 卡挂载rootfs。

#### NFS 挂载

1.将根目录openwrt-leo-gx8010-ssd-1v-rootfs.tar.gz 解压到主机的nfs 路径下，比如  
/opt/rootfs。

2.配置uboot 的nfs 启动参数nfsboot（默认已配好）：

setenv nfsboot “setenv bootargs console=ttyS0,115200

init=/init root=/dev/nfs

rw nfsroot=192.168.xxx.xxx:/opt/rootfs

ip=192.168.xxx.yyy;bootz ${loadaddr} - ${fdt\_addr}”

需要注意的是 nfsroot 是主机的ip 及rootfs 路径，ip 是开发板的ip，根据实际情况修改，执行run nfsboot 启动系统并从主机nfs 目录挂载rootfs。

Note：上述变量结尾不存在换行，只是分行显示的原因。

#### NAND 模式

如果flash 是NAND，则需要将根目录烧录到NAND 上对应的分区（mtd7）。

1.配置启动参数（默认已配好）：

nandboot=setenv bootargs console=ttyS0,115200 ${mtdpart}

${mtdargs};bootz ${kernel\_addr} - ${fdt\_addr}

mtdpart=mtdparts=spi32766.0:1m\(scpu\),256k\(uboot\_stage1\),256k

\(uboot\_env\),512k\(uboot\_stage2\),512k\(boot\_music\),512k\(kernel\_

dtb\),7m\(kernel\),118m\(rootfs\)

mtdargs=ubi.mtd=7,2048 root=ubi0:rootfs rootwait

rootfstype=ubifs rw loglevel=6

mtdpart 为 uboot 将分区信息传递给Kernel，‘118m（rootfs）’为 rootfs 分区，前面为分区大小，mtdargs 中的‘ubi.mtd=7’与上面 mtdpart 的 rootfs 分区号必须一致， 否则无法挂载根文件系统。

2.使用nfs 工作模式挂载根目录。

3.把需要烧写的openwrt-leo-gx8010-ssd-1v-ubi.img 拷贝到已经挂载完成的根目录上，之后使用ubi 命令将其烧写到mtd7 分区：

ubiformat /dev/mtd7 -f openwrt-leo-gx8010-ssd-1v-ubi.img

---

## 启动流程总结

* MCU 初始化部分硬件后会加载uboot stage1 到SRAM 中，uboot stage1运行起来以后会将DDR 初始化，然后将uboot stage2 加载到DDR 中，并跳转到DDR 执行。uboot stage2 代码运行起来以后，会将板级相关硬件初始化，然后加载Kernel。

* 加载Kernel 和加载rootfs。

Kernel 加载方式：netload，flashload（norload、nandload）

rootfs 加载方式：mmcboot，nfsboot，nandboot

不同的组合对应不同的启动方式

1.从NOR Flash 加载Kernel 和dtb，从SD 卡加载rootfs 并启动

bootfnor=run norload;run mmcboot

2.从NAND Flash 加载Kernel、dtb 和rootfs 并启动

bootnand=run nandload;run nandboot

3.从tftp 加载Kernel 和dtb，从SD 卡加载rootfs 并启动

bootnet=run netload;run mmcboot

4.从tftp 加载Kernel 和dtb，从NAND Flash 加载rootfs 并启动

bootnet=run netload;run nandboot

以上的rootfs 均从flash 启动，当然也可以从nfs 加载rootfs 并启动。

bootcmd 是默认的启动命令，例如

bootcmd=run netload;run mmcboot

默认从tftp 加载Kernel 和dtb，从SD 卡加载rootfs 并启动，开发者可修  
改此值为想要的启动方式。

