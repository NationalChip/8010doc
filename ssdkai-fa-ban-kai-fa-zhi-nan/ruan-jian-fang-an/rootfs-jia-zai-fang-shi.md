# Rootfs 加载方式

### SPI + SD 卡

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

### NFS 挂载

1.将根目录openwrt-leo-gx8010-ssd-1v-rootfs.tar.gz 解压到主机的nfs 路径下，比如  
/opt/rootfs。

2.配置uboot 的nfs 启动参数nfsboot（默认已配好）：

setenv nfsboot “setenv bootargs console=ttyS0,115200

init=/init root=/dev/nfs

rw nfsroot=192.168.xxx.xxx:/opt/rootfs

ip=192.168.xxx.yyy;bootz ${loadaddr} - ${fdt\_addr}”

需要注意的是 nfsroot 是主机的ip 及rootfs 路径，ip 是开发板的ip，根据实际情况修改，执行run nfsboot 启动系统并从主机nfs 目录挂载rootfs。

Note：上述变量结尾不存在换行，只是分行显示的原因。

### NAND 模式

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

