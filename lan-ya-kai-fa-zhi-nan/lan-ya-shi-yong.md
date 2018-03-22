# 蓝牙使用

**打造蓝牙音箱（A2DP Sink）**

第一步: 通过串口或ssh登录板子终端

---

第二步: 输入命令bluetoothctl，在bluetoothctl命令行下：

输入power on，激活蓝牙.

输入default-agent，用于管理pin码的接收与发送.

agent on

输入discoverable on，使设备可以被手机发现.

1.当有手机请求配对需确认pin码,比如

\[agent\] Confirm passkey 736537 \(yes/no\):

```
输入yes

```

2.配对成功后，连接过程会出现服务请求认证，比如A2DP服务：

\[agent\] Authorize service 0000110d-0000-1000-8000-00805f9b34fb \(yes/no\)：

```
输入yes

TIPS:多个服务需分别确认，可以使用trust命令来添加信任设备的MAC，以免除每次的认证确认：

trust EC:3D:FD:3D:D2:47

```

3.连接成功后，Bluetoothctl的命令行名字会变为手机的名称

---

第三步-与第二部交互的操作:

打开手机蓝牙 --&gt; 进入设置界面.

点击 gxrobot，确认配对，等待连接.

---

第四步: 连接成功后，打开手机上的音乐软件播放音乐，就可以通过智能音箱听到音乐了.

---

**打造蓝牙播放器（A2DP Source）**

第一步: 通过串口或ssh登录板子终端.

---

第二步: 输入命令bluetoothctl，在bluetoothctl命令行下：

输入power on，激活蓝牙.

1.待蓝牙音箱打开后，输入scan on，开启扫描.

2.发现蓝牙音箱的设备MAC地址后，可输入scan off关闭扫描.

3.连接音箱，输入connect 11:58:02:B4:02:50（蓝牙音箱的MAC地址）

connect 2C:57:31:5E:4C:77

4.待连接成功后，bluetoothctl的命令行名字会变为对应的蓝牙音箱名字.

---

第三步: 配置pulseaudio a2dp sink，可再打开一个终端来操作

输入pactl list，找到对应的sink设备，比如:

Card \#0

```
Name: bluez\_card.11\_58\_02\_B4\_02\_50

Driver: module-bluez5-device.c

Owner Module: 13

Properties:

    device.description = "HAVIT M8"

    device.string = "11:58:02:B4:02:50"

    device.api = "bluez"

    device.class = "sound"

    device.bus = "bluetooth"

    device.form\_factor = "headset"

    bluez.path = "/org/bluez/hci0/dev\_11\_58\_02\_B4\_02\_50"

    bluez.class = "0x240404"

    bluez.alias = "HAVIT M8"

    device.icon\_name = "audio-headset-bluetooth"

    device.intended\_roles = "phone"

Profiles:

    headset\_head\_unit: Headset Head Unit \(HSP/HFP\) \(sinks: 1, sources: 1, priority: 20, available: yes\)

    a2dp\_sink: High Fidelity Playback \(A2DP Sink\) \(sinks: 1, sources: 0, priority: 10, available: yes\)

    off: Off \(sinks: 0, sources: 0, priority: 0, available: yes\)

Active Profile: off

Ports:

    headset-output: Headset \(priority: 0, latency offset: 0 usec\)

        Part of profile\(s\): headset\_head\_unit, a2dp\_sink

    headset-input: Headset \(priority: 0, latency offset: 0 usec\)

        Part of profile\(s\): headset\_head\_unit

```

如果看到“Active Profile: off” ，需激活a2dp profile:

pactl set-card-profile 0 a2dp\_sink

0 表示 Card \#0，表示的对应的声卡编号.

使用pactl info 查看系统输出的声卡设备.

```
Server String: /var/run/pulse/native

```

Library Protocol Version: 32

Server Protocol Version: 32

Is Local: yes

Client Index: 13

Tile Size: 4056

User Name: pulse

Host Name: OpenWrt

Server Name: pulseaudio

Server Version: 10.0

Default Sample Specification: s16le 2ch 44100Hz

Default Channel Map: front-left,front-right

Default Sink: bluez\_sink.11\_58\_02\_B4\_02\_50.a2dp\_sink

Default Source: bluez\_sink.11\_58\_02\_B4\_02\_50.a2dp\_sink.monitor

Cookie: 372e:4f33

如果Default Sink不是蓝牙声卡 “bluez\_sink.11\_58\_02\_B4\_02\_50.a2dp\_sink”，需切换Default Sink：

pactl set-default-sink bluez\_sink.11\_58\_02\_B4\_02\_50.a2dp\_sink

11\_58\_02\_B4\_02\_50 是蓝牙音箱的MAC地址.

---

第四步: 使用智能音箱自带的播放器mpc播放相应的歌曲.

输入 mpc add /media/music/hongdou.mp3 将歌曲加入播放列表.

输入 mpc play 播放音乐.

