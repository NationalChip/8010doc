# 编译器安装

首次安装：

`pip install -i https://testpypi.python.org/pypi npu_compiler`

升级安装：

`pip install —upgrade -i https://testpypi.python.org/pypi npu_compiler`

## 模型文件准备 {#模型文件准备}

* 准备TensorFlow生成的PB文件，和其对应的CKPT文件
* 通过TensorFlow提供的freeze\_graph.py将PB与CKPT合并，生成新的PB文件

## 编译选项 {#编译选项}

编译之前需要配置config.yaml，具体选项为：

| 项目 | 选项 | 说明 |
| :--- | :--- | :--- |
| PB\_FILE |  | 包含CKPT的PB文件名和路径 |
| OUTPUT\_FILE |  | NPU编译后输出文件名 |
| SECURE | true / false | 是否对模型加密（还未支持） |
| NPU\_UNIT | NPU32 / NPU64 | 选择NPU型号对应的MAC数量（SNPU选32，主NPU选64） |
| COMPRESS | true / false | 是否启动压缩模式 |
| COMPRESS\_QUANT\_BITS | 4/5/6/7/8/9/10 | 量化压缩的最终bit数 |
| FLOAT16 | true／false | 是否启用FLOAT16量化模式 |
| FLOAT16\_EXP\_BITS | 3/4/5/6 | 选择FLOAT16指数位的Bit数（默认5） |
| OUTPUT\_TYPE | jason/raw | 目前请选择raw格式 |
| INPUT\_OPS | OP\_NAME: \[shape\] | 设置输入的OP名字和SHAPE |
| OUTPUT\_OPS | \[OUT\_OP\_NAMES, ... \] | 设置输出OP的名字列表 |

#### 配置文件例子： {#配置文件例子：}

**kws\_config.yaml**

`PB_FILE: kws_i320_norm_cnn32_lstm96x2_with_ckpt.pb`

`OUTPUT_FILE: kws_i320_norm_cnn32_lstm96x2_NCHW_fp32_0626.npu`

`SECURE: false # true/false`

`NPU_UNIT: NPU32 # NPU16/NPU32/NPU64`

`COMPRESS: true # true/false`

`COMPRESS_QUANT_BITS: 6 # 4/5/6/7/8/9/10`

`FLOAT16: true # true/false`

`FLOAT16_EXP_BITS: 5 #3/4/5/6`

`OUTPUT_TYPE: raw # json/raw`

`INPUT_OPS:`

`Feats: [1, 1, 320]`

`State_c0: [1, 96]`

`State_h0: [1, 96]`

`State_c1: [1, 96]`

`State_h1: [1, 96]`

`OUTPUT_OPS: [Model/rnn_out, State_c0_out, State_h0_out, State_c1_out, State_h1_out]`

