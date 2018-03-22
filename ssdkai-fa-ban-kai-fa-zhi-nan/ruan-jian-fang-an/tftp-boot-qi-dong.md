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



