# 产品简介

## 芯片简介

* 支持2/4/6/8路麦克风阵列

* 集成8通道ADC/I2S/PDM，支持模拟麦和数字麦

* 内置DSP，支持降噪、AEC、Beamforming、去混响等算法

* 低功耗离线语音激活

* 离线语音识别

* 超低功耗待机，支持电池供电

* 对接主流语音云平台

* Linux操作系统

* 典型配置：128MB SPI NAND Flash+内置128MB DDR

---

## 方案设计简介

![](/assets/import.png)

```
                            GX8010硬件系统概图
```

#### 电源

8.4V 2200mAh 锂电池（便携模式）

12V/1A AC/DC电源适配器（调试模式）

#### 存储器

SPI NOR Flash:：单颗8MByte ~ 16MByte（64Mbit ~ 128Mbit）

SPI NAND Flash：单颗 1GBit~2GBit

#### 音频

模拟音频：Audio Right/Left，模拟音频输出

#### 多媒体

USB：一路USB 2.0 HOST接口；一路USB Slave接口

#### MIC阵列

支持最高8路模拟MIC输入，支持单端和差分模式，每路自带BIAS电源；SSD平台使用6路MIC输入，单端模式。

#### 用户接口

2组SDIO接口，支持SDIO格式设备，SSD平台使用TF卡和WIFI&BT模块。

#### 调试接口

串口：3路UART接口（CKCore，DSP，ARM）

#### LED接口

GX8010 支持多种型号LED Driver IC，通过I2C接口控制LED的变化。SSD平台采用 31FL3236A LED Driver IC，可以驱动多达 36 路LED，目前MIC板共有 12 枚RGB的LED，组成LED阵列。

#### 功放电路

SSD平台使用TI的TPA 3132D2 D类高效音频功率放大器，在19V 供电下可以输出 2\*42W（4 欧姆）的功率，采用 2 节锂电池（8.4V）供电下能也能输出 2\*8W（4 欧姆）的正弦功率，足以轻松驱动市面上大多数的全频喇叭；TPA3132D2的替代型号SY6018可以帮助客户降低方案成本。

