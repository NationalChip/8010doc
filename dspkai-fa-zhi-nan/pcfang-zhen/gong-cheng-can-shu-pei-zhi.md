# 工程参数配置 {#5-工程参数配置}

## 宏配置 {#51-宏配置}

1. 调试算法时需要将 vsp\_main.c 中的宏 CALC\_PROFILE 关闭
2. 配置 mic 数跟 ref 数：修改 vsp\_main.c 中的宏`MIC_NUM（`默认为4） 和`REF_NUM（`默认为2）
3. 配置 context 和 channel 数：修改 vsp\_context.c 中的宏`FRAME_NUM_PER_CONTEXT（`默认为3）和`CONTEXT_NUM_PER_CHANNEL（`默认为5）
4. 配置帧长：修改 vsp\_context.c 中的宏`FRAME_LENGTH（`默认为10）， 单位是 ms

## 输出文件 {#52-输出文件}

tb/common/vsp\_context.c 中有个函数 VspWriteWaveData 是会在 VspProcessActive 后执行它，它的功能是将处理后的音频数据（out\_buffer）写入文件中。这个函数可以自行修改实现自己的目的。

