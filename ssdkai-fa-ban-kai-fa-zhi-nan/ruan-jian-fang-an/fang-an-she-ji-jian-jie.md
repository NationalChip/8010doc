# **方案设计简介**

智能音箱方案使用OpenWRT 来编译所有模块，生成所需要的固件和根文件系统，OpenWRT 整体软件框架如下图：

![](/assets/OpenWRT 软件框架.png)

```
                               OpenWRT 软件框架
```

其中第一行为原始目录，第二行为编译过程中生成的目录，我们将Senseflow、Mpd 和Uboot 设计为package 下的软件包，通过Makefile 来定义软件包版本、下载地址、编译方式和安装地址等。在target 里定义firmware 和Kernel 的编译过程。编译完成后firmware和ipk都会放在bin 目录对应的板级内。

### FLASH 分区

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



