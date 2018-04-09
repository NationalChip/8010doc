# 工具链安装

## 概述

本文描述了 Xtensa Development Tools 的安装。

## **安装工具链**

#### 1. 准备工作

由于Xplorer没有64bit版本，如果你的操作系统是Linux 64bit的，需要先安装32bit的兼容包。

* 以下操作在Ubuntu版本14.04.4 LTS，内核版本4.2.0-27-generic上验证成功
  ```
  sudo dpkg --add-architecture i386
  sudo apt-get update
  sudo apt-get install libc6:i386 libncurses5:i386 libstdc++6:i386
  sudo apt-get install libx11-6:i386
  sudo apt-get install gtk2-engines:i386
  sudo apt-get install lib32ncurses5 lib32z1
  sudo apt-get install libxtst6:i386
  sudo apt-get install libgtk2.0-0:i386
  sudo apt-get install lib32ncurses5
  sudo apt-get install libcanberra-gtk3-0:i386

  ```

以下操作在Ubuntu16.04.1 LTS 64bit，内核版本4.4.0-31-generic上验证成功。

```
sudo dpkg --add-architecture i386
sudo apt-get update
sudo apt-get install libc6:i386 libncurses5:i386 libstdc++6:i386
sudo apt-get install libx11-6:i386
sudo apt-get install gtk2-engines:i386
sudo apt-get install lib32ncurses5 lib32z1
sudo apt-get install libxtst6:i386
sudo apt-get install libgtk2.0-0:i386
sudo apt-get install lib32ncurses5

```

#### 2. 安装

```
chmod +x Xplorer-7.0.8-linux-installer.bin
./Xplorer-7.0.8-linux-installer.bin

```

一路按提示安装即可。安装路劲建议选择默认路径，即当前用户目录。安装完成后桌面会有 RUN Xplorer 7.0.8 的快捷方式 注意：如果上述安装过程中提示“段错误 \(核心已转储\)”。可以尝试：`./Xplorer-7.0.8-linux-installer.bin --mode text`

#### 3. 添加 License

双击桌面的 RUN Xplorer 7.0.8，打开 Xplorer, 点击菜单栏的 "Help" - "Xplorer License Keys"，在弹出的对话框上点击"Install Software Keys"，输入License \(请向国芯FAE索取），点击"Finish"。 如果可以看到图中红框提示就表示添加成功。如下图：![](/assets/license_ok.png)

#### 4. 添加CORE

我们目前使用的DSP processor core是GXHifi4\_170719A\_G1708，需要手动添加到Xplorer中。

右键点击"System Overview"窗口中的"Configurations"，选择"Find and Install a Configuration Build"，在弹出的窗口中点击"Browser"并选择configuration文件（linux为GXHifi4\_170719A\_G1708\_linux\_redist.tgz），点击"OK"。 添加完成后，可以在"System Overview“窗口的"Configurations"中找到刚添加的"GXHifi4\_170719A\_G1708"，如下图红框所示。![](/assets/nre_core_ok.png)

#### 5. 环境配置

为了方便在命令行使用编译工具，可以把工具链添加到系统PATH路径下。+

~/.bashrc中添加：

```
export XTENSA_PATH=/home/user/xtensa # 这里的 user 是当前用户，默认安装位置
export PATH="$PATH:${XTENSA_PATH}/XtDevTools/install/tools/RG-2017.8-linux/XtensaTools/bin" #其中XTENSA_PATH为xtensa的安装路径

```

在使用工具链时，需要指定core的名称和路径，可以通过环境变量来指定。 ~/.bashrc中添加：

```
export XTENSA_CORE=GXHifi4_161020A_G1707
export XTENSA_SYSTEM=${XTENSA_PATH}/XtDevTools/install/builds/RG-2017.8-linux/${XTENSA_CORE}/config #其中XTENSA_PATH为xtensa的安装路径

```

#### 6. 最后

工具链安装完成，最好是重启电脑。开始阅读[vsp\_simulate 使用指南](https://nationalchip.gitbooks.io/vsp_simulate/content/)，进行算法移植、优化和开发

