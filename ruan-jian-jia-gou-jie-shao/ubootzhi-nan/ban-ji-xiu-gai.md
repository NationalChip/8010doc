# 板级修改

**如何修改uboot在flash的分区位置**

uboot stage1在flash的位置，由scpu工程设置

uboot stage2在flash位置在板级头文件中即include/configs/${SYS\_CONFIG\_NAME:"%"=%}.h

......

\#define CONFIG\_SYS\_SPI\_U\_BOOT\_OFFS 0x180000

---

**从哪里加载环境变量?**

环境变量在flash的位置，对应的板级头文件，如：include/configs/leo\_mpw\_dev\_2v.h

......

/\* Environment for SPI boot \*/

\#ifndef CONFIG\_ENV\_OFFSET

\#ifdef CONFIG\_ENV\_IS\_IN\_SPI\_FLASH

\#define CONFIG\_ENV\_OFFSET \(0x00140000\)

\#define CONFIG\_ENV\_SECT\_SIZE \(64 \* 1024\)

\#elif defined\(CONFIG\_ENV\_IS\_IN\_SPI\_NAND\)

\#define CONFIG\_ENV\_OFFSET \(0x00140000\)

\#define CONFIG\_ENV\_SECT\_SIZE \(128 \* 1024\)

\#endif

\#endif

......

---

**选择在那种介质加载env，板级头文件中定义**

/\*

\* U-Boot environment

\*/

\#ifdef CONFIG\_SPL\_SPI\_FLASH\_SUPPORT

\#define CONFIG\_ENV\_IS\_IN\_SPI\_FLASH // 表示env在spi nor flash中

\#elif defined\(CONFIG\_SPL\_SPINAND\_SUPPORT\)

\#define CONFIG\_ENV\_IS\_IN\_SPI\_NAND // 表示env在spi nand flash中

\#else

\#define CONFIG\_ENV\_IS\_IN\_NVRAM

\#endif

---

**uboot加载env代码在哪？**

common/board\_r.c +875 的 initr\_env函数中调用

---

**uboot中的相关环境变量的介绍**

kernel\_addr : kernel加载到DDR的地址

kernel\_offs : kernel在flash中的偏移量

kernel\_size : kernel的zImage大小

fdt\_addr : device tree在ddr的位置

fdt\_offs : device tree在flash中的偏移量

fdt\_size : device tree的大小

bootimage : kernel image文件名称

fdtimage : device tree文件的名称

norburn : 使用tftb将kernel + dtb下载到nor flash中

nandburn : 使用tftb将kernel + dtb下载到nand flash中

nandboot : 从nand中启动kernel

ramboot : 从ram中启动kernel

nfsboot : nfs启动kernel

netload : 用网络的方式加载kernel + btb

nandload : 从nand中加载kernel + dtb

norload : 从bor中加载kernel + dtb

mtdargs : 挂载ubifs时需要的参数+

mtdpart : mtd分区表

