# bootx工具使用简介 {#使用简介}

目前我们进行mcu.bin,uboot的烧录，是通过 pc linux 下的bootx 工具进行烧录的。

烧录前，确保开发板的ck\_uart串口连接到了电脑上

---

**命令格式**

bootx \[-m &lt;machine&gt;\] \[-b &lt;bootloader file&gt;\] \[-c &lt;boot command line&gt;\] \[-d &lt;serial device&gt;\] \[-r &lt;rom baudrate&gt;\] \[-h\] \[-v\] \[-V\]

---

**参数说明**

-m： bootx工具会自动读取~/.bootx目录下的.boot文件生成-m machine list，用户通过-m选项选择-m machine list显示的板级

-b： 指定外部的.boot文件

-c： 选择-c command list中的命令

-d： 指定具体的串口设备名，如果未指定，bootx 会自动扫描一个可用的串口

-r： 用于指定与rom通信的波特率，如115200/57600等，若不指定，使用.boot文件中配置的波特率

-h： 显示帮助信息

-v： 显示bootx版本信息

-V： 显示bootx的changelog

---

**使用例程**

**serialdown**

下载download.bin到flash的0x0地址处

bootx -b gxscpu.boot -c serialdown 0x0 download.bin

**flash erase**

将flash上地址为0x0长度为0x100的数据擦除，实际上将会以64KB为单位进行擦除

bootx -b leo\_mpw\_ssd\_1v.boot -c flash erase 0x0 0x10000

**连续命令**

先将flash上数据全部擦除，然后将download.bin下载到flash起始地址处

bootx -m leo\_mpw\_dev\_2v -c "flash eraseall;serialdown 0 gxscpu.bin"

---

常见问题

下载过程中打印出如下信息基本是.boot文件有问题或者串口传输数据有问题，首先确定.boot文件和板级是否对应，串口是否正常。

"BootROM error: wait for "GET",please check stb UART receive !"

"data transmit error, try agin ..."

"error, please try agin."

"Before exec the first command, failed to receive \"boot&gt;\" from stb in 3s, will EXIT\_FAILURE"

使用可靠的bootx工具和.boot文件，minicom可以输入输出，但bootx工具不能下载，可能是电源供电问题：

12V的板子使用了5V电源。

12V的板子选用了12V但电流很小的电源。

