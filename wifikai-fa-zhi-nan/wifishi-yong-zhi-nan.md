# WIFI使用指南

Wifi驱动加载：/etc/rc.d/S30wlanbt-8723bs

```
\#!/bin/ash

MODULE\_PATH=/lib/modules/4.4.25

echo "load 8723bs.ko"

insmod ${MODULE\_PATH}/8723bs.ko

```

---

kernel启动会打印wifi驱动成功，设备识别：

\[ 38.690000\] RTL871X: module init start

\[ 38.700000\] RTL871X: rtl8723bs v4.4.0\_17166.20160322\_BTCOEX20151223-654a

\[ 38.700000\] RTL871X: build time: Dec 24 2017 14:57:55

\[ 38.710000\] RTL871X: rtl8723bs BT-Coex version = BTCOEX20151223-654a

\[ 38.750000\] RTL871X: hal\_com\_config\_channel\_plan chplan:0x20

\[ 38.760000\] RTL871X: rtw\_ndev\_init\(wlan0\) if1 mac\_addr=ec:3d:fd:3d:d2:4c

\[ 38.770000\] RTL871X: module init ret=0

---

手动测试wifi的话，比如在根目录下准备好 wlan.sh 和 wpa\_supplicant.conf 两个文件，然后在shell中执行：./wlan.sh，就启动wifi了

Wifi测试脚本：

wlan.sh

```
\#!/bin/bash

WIFI\_DEV="wlan0"

\#ifconfig wlan0 up

wpa\_supplicant -i${WIFI\_DEV} -Dnl80211 -c ./wpa\_supplicant.conf 
&
udhcpc -i ${WIFI\_DEV}

echo "nameserver 8.8.8.8" 
&
gt; /etc/resolv.conf

```

---

wpa\_supplicant.conf

```
ctrl\_interface=/var/run/wpa\_supplicant

network={

```

```
ssid="GUOXINSW"

proto=WPA WPA2

key\_mgmt=WPA-PSK

pairwise=CCMP TKIP

group=CCMP TKIP

psk="guoxinsz"

priority=2

```

```
}
```



