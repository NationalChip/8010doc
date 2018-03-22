# 启动流程总结

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

默认从tftp 加载Kernel 和dtb，从SD 卡加载rootfs 并启动，开发者可修改此值为想要的启动方式。

