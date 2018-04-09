# GX8010和GX8008烧录工具使用指南 {#gx8010和gx8008烧录工具使用指南}

Bootx支持串口，usb slave两种方式下载

工具下载地址： 链接：[https://pan.baidu.com/s/1jmWUZXnwgWY24oo9n-sUEA](https://pan.baidu.com/s/1jmWUZXnwgWY24oo9n-sUEA)密码：4fwp

## 一、 {#一、}

进行串口下载的时候，确保串口线接在uart ck上面，确认上电有输出。配置是默认的115200

串口烧录命令：（后面烧录的文件名根据自己的实际情况修改）

bootx -b gxscpu.boot -t s -c serialdown 0x0 mcu.bin

/bootx -b gxscpu.boot -t s -c serialdown 0x100000 u-boot-spl.bin

./bootx -b gxscpu.boot -t s -c serialdown 0x180000 u-boot.img

./bootx -b gxscpu.boot -t s -c serialdown 0x280000 leo\_gx8010\_ssd\_1v.dtb

按照提示要求重新上电

（使用8008的时候，使用该方法）

---

**注意：如果出现打开设备结点失败的情况时，需要 sudo 去执行**

---

![](/assets/usb.jpg)

## 二、 {#二、}

usb slave下载，这个速度更快

解压usb\_app\_and\_driver\_2018\_02\_28.tar.gz

PC机上进行编译：（目前仅仅支持linux，还不支持windows）

cd usb\_driver

make

然后，insmod usb-skeleton.ko

在开发板上把串口线接到uart ck上面，并确认开发板usb slave也接到了电脑上

![](/assets/uart.jpg)

执行：./download.sh 0

0代表ttyusb0,如果识别的串口是ttyusb1，就修改为: /download.sh 1

如果文件系统异常，擦除文件系统区域下，再烧：

sudo ./bootx -b ./gxscpu.boot -c flash erase 0xa00000 0x761d800 -d /dev/ttyUSB0

或者整个擦除：

sudo ./bootx -b ./gxscpu.boot -c flash erase 0x000000 0x8000000 -d /dev/ttyUSB0

烧录完成后，进uboot下，输入：

setenv bootcmd "run bootnand"

saveenv

重启，就可以自动进nandflash启动 了

## 三 、bootx命令格式 {#三-、bootx命令格式}

bootx \[-m &lt;machine&gt;\] \[-t &lt;transfer mode&gt;\] \[-b &lt;bootloader file&gt;\] \[-c &lt;boot command line&gt;\] \[-d &lt;serial device&gt;\] \[-r &lt;rom baudrate&gt;\] \[-D\] \[-h\] \[-H\] \[-v\] \[-V\]

* -m： bootx工具会自动读取~/.bootx目录下的.boot文件生成-m machine list，用户通过-m选项选择-m machine list显示的板级

* -b： 指定外部的.boot文件

* -c： 选择-c command list中的命令

* -d： 指定具体的串口设备名，如果未指定，bootx 会自动扫描一个可用的串口

* -r： 用于指定与rom通信的波特率，如115200/57600等，若不指定，使用.boot文件中配置的波特率

* -t： 配置下载 .boot 文件时的传输方式，现在有三种选择 s/u/su，不用 -t 选项时，默认使用串口下载

* s : 完全使用串口下载 .boot 文件

* u : 完全使用 usbslave 下载 .boot 文件，如果要使用该模式，使用前一定要加载 pc/host 端的 usb slave 的驱动才能正常使用

* su : 先用串口下一部分 .boot 文件，再用 usb slave 下剩余的 .boot 文件，这个模式下需要板子同时有串口设备和 usb 数据 线连接。这个下载方式主要是针对板子上没有 boot 按键的，没有 boot 按键就无法默认从 usb slave 启动。如果要使用该模式，使用前一定要加载 pc/host 端的 usb slave 的驱动才能正常使用

* -D： 进入 debug 模式，用于处理在下载过程中遇到问题时，能过通过该模式完成下载失败的问题的定位。当前主要用于当传输模式为 su 时，如果下载失败，可通过配合该选项去获取调试信息

* -h： 显示帮助信息

* -H： 会显示更加详细的使用方法和使用例程

* -v： 显示bootx版本信息

* -V： 显示bootx的changelog

## 四 、使用例程 {#四-、使用例程}

下载命令:

serialdown（需要传输模式 -t 为 s）

下载download.bin到flash的0x0地址处

```
bootx -b gxscpu.boot -t s -c serialdown 0x0 download.bin

```

usbslavedown（需要传输模式 -t 为 u/su）

下载download.bin到flash的0x0地址处，以 -t 为 u 的情况举例如下

```
bootx -b gxscpu.boot -t u -c usbslavedown 0x0 download.bin

```

dump 命令:

serialdump（需要传输模式 -t 为 s）

从 flash 中读取起始地址为 0 长度为 0x100000 的数据到当前目录的 dump.bin 文件中

```
bootx -b gxscpu.boot -t s -c serialdump 0  0x100000 dump.bin

```

usbslavedump（需要传输模式 -t 为 u/su）

从 flash 中读取起始地址为 0 长度为 0x100000 的数据到当前目录的 dump.bin 文件中，以 -t 为 u 的情况举例如下

bootx -b gxscpu.boot -t u -c usbslavedump 0 0x100000 dump.bin

通用命令:

flash 擦除

部分擦除：

将flash上地址为0x0长度为0x100的数据擦除，实际上将会以64KB为单位进行擦除

```
bootx -b leo\_mpw\_ssd\_1v.boot -c flash erase 0x0 0x10000

```

全部擦除：

```
bootx -b leo\_mpw\_ssd\_1v.boot -c flash eraseall

```

一次使用多组命令的方法:

将 -c 后的所有命令使用双引号引起来，每一组完整的命令以分号结尾，最后一组命令不必加分号

先将flash上数据全部擦除，然后将download.bin下载到flash起始地址处

```
bootx -m leo\_mpw\_dev\_2v -c "flash eraseall;serialdown 0 gxscpu.bin"
```



