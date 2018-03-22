# 蓝牙调试

**bluez在openwrt中的编译**

蓝牙使用bluez工具

编译：

make package/bluez/clean

make package/bluez/compile V=s

---

**对应的工具，放在/usr/bin目录中，有**：

bluemoon

bluetoothctl

bluetoothd

btmgmt

btmon

---

**启动：**

在/ect/rc.d的目录中，打开：S30wlanbt-8723bs

echo "load 8723 bt firmware"

/usr/sbin/rtk\_hciattach -n -s 115200 /dev/ttyS1 rtk\_h5 &

---

**debug**

bluez luez 官方有提供hcidump，可以使用 hcidump目录下stackhost 与 controller之间的 蓝牙数据交互流程

hcidump工具可以直接运行，将 loglog 打印在terminal，也可以用下方式将 logloglog保存在文件

保存在文件 ：

hcidump –t&gt;x.txt

hcidump –w x.txt

