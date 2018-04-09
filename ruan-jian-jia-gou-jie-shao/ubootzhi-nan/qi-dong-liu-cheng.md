# 启动流程

1. 系统上电后执行iROM的代码iROM中检测OM pin选择启动介质（serial/usb/nand/nor ...），从启动介质将bootloader的stage1加载到内 部SRAM中，并跳转到SRAM中执行。
2. 跳转到sram中后，需要对部分硬件进行初始化（最关键是初始化DDR），然后将bootloader的stage2加载到DDR中去，然后跳转到DDR 执行stage2。
3. 在bootlaoder stage2中对板级的相关硬件进行初始化，然后将kernel加载到DDR中，执行kernel代码，那么接下来就没bootload什么事情了。

---

![](/assets/GX8010.png)

1. 系统上电执行iROM的代码，iROM中的代码会按顺序尝试从（serial、spi nor、spi nand ...）启动。iROM是通过gxscpu，bin文件的头信息来判断是什么介质（0xaa55aa55-spi nor, 0xbb55bb55-spi nand）。并且只加载gxscpu.bin的前8k代码到csky的SRAM中执行（我们把这部分代码叫做ck stage1）。
2. gxscpu.bin的前8k代码初始化部分硬件后，然后把剩余的代码加载到SRAM中来（我们把这部分代码叫做ck stage2）。
3. 当ck stage2运行起来以后，回去flash中加载uboot stage1到ARM的SRAM中，然后将RAM核启动（这时候CK ARM两个核心就同时运行了）。
4. uboot stage1运行起来以后会将DDR初始化，然后将uboot stage2加载到DDR中，并跳转到DDR执行。
5. uboot stage2代码运行起来以后，会将板级相关硬件初始化，然后将kernel加载到DDR中去，并跳转去执行kernel的代码，这个时候uboot就寿终正寝了。
6. kernel启动后，将文件系统挂载成功后，整个启动过程就算完成了。

---

**启动过程stage1做了哪些事情**

关闭中断

安装异常向量表

关闭cache, 关闭mmu

设置时钟，设置栈，清bss段

串口初始化，内存初始化

加载uboot stage2

---

**uboot stage1源码分析**

├─── reset\(\)

├── cpu\_init\_cp15\(\)

├── cpu\_init\_crit\(\)

│ ├── lowlevel\_init\(\) // vi arch/arm/cpu/armv7/lowlevel\_init.S +1

├── \_main\(\) // vi arch/arm/lib/crt0.S +67

├── board\_init\_f\_alloc\_reserve\(\) // 为gd\_t预留内存

├── board\_init\_f\_init\_reserve\(\) // 清空gd, 为malloc设置基地在

├── board\_init\_f\(\) // vi /arch/arm/mach-leo\_mpw/spl.c +101

│ ├── leo\_pll\_init\(\) // 系统时钟配置

│ ├── timer\_init\(\) // 系统时间初始化

│ ├── sysmgr\_pinmux\_init\(\) // 管脚复用配置

│ ├── preloader\_console\_init\(\) // 串口初始化

│ ├── sdram\_mmr\_init\_full\(\) // DDR初始化

│ ├── leo\_resume\(\) // 休眠状态情况下唤醒

├── spl\_relocate\_stack\_gd\(\) // 将gd重定位到DDR中

├── board\_init\_r\(\) // vi common/spl/spl.c +427

├── timer\_init\(\) // 系统时间初始化

├── board\_boot\_order\(\) // 启动顺序设置

├── spl\_load\_image\(\) // 加载uboot stage2

├── jump\_to\_image\_no\_args\(\) // 跳到uboot stage2运行

---

**uboot stage2源码分析**

├─── reset\(\)

├── cpu\_init\_cp15\(\)

├── cpu\_init\_crit\(\)

│ ├── lowlevel\_init\(\) // vi arch/arm/cpu/armv7/lowlevel\_init.S +18

├── \_main\(\) // vi arch/arm/lib/crt0.S +67

├── board\_init\_f\_alloc\_reserve\(\) // 为gd\_t预留内存

├── board\_init\_f\_init\_reserve\(\) // 清空gd, 为malloc设置基地在

├── board\_init\_f\(\) // vi common/board\_f.c +1051

│ │ ......

│ ├── dram\_init\(\) // gd 中设置了ram size

│ ├── setup\_dest\_addr\(\)

│ ├── reserve\_mmu\(\) // 为MMU TLB 预留空间

│ ├── reserve\_uboot\(\) // 为uboot代码预留空间

│ ├── reserve\_malloc\(\) // 为malloc预留空间

│ ├── reserve\_board\(\) // 为bd\_t，板级信息预留空间

│ ├── reserve\_global\_data\(\) // 为gd\_t预留空间

│ ├── reserve\_fdt\(\) // 为uboot device tree预留空间

│ ├── reserve\_stacks\(\) // 为栈预留空间

│ ├── setup\_reloc\(\) // 为代码重定位做准备

│ │ ......

├── relocate\_code\(\) // vi /arch/arm/lib/relocate.S +79

├── relocate\_vectors\(\) // 设置cp15的中断向量表

├── c\_runtime\_cpu\_setup\(\) // 关闭cache

├── board\_init\_r\(\) // vi uboot/common/board\_r.c +982

│ ......

├── initr\_mmc\(\)

│ ......

├── initr\_env\(\)

│ ......+

├── run\_main\_loop\(\)

