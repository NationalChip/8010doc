# **固件烧写**

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

