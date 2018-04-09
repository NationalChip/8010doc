# NPU模拟器使用

首次安装：

pip install npu\_compiler

升级安装：

pip install --upgrade npu\_compiler

查看工具链版本:

gxnpuc –version

目前匹配的python版本是：python2.7和python3.5

注意：安装过程中如果需要部分python的第三方包，比如numpy,tensorflow等，请按照错误提示补充安装。例如：sudo pip install numpy

编译器支持tensorflow的版本是：0.10-1.3

---

工具链使用 gxnpuc:

用于把模型文件编译成能在 NPU 上运行的npu 文件。

usage: gxnpuc \[-h\] \[-V\] \[-L\] \[config\_filename\]

optional arguments:

-h, --help show this help message and exit

-V, --version show program's version number and exit

-L, --list list supported ops

---

gxnpudebug:

如果编译时配置文件中的DEBUG\_INFO\_ENABLE 选项设置为true，编译出的npu 文件

带上了调试信息，此时可以使用调试工具 gxnpudebug 工具来处理该文件。

usage: gxnpudebug \[-h\] \[-S\] \[-P\] file \[file ...\]

optional arguments:

-h, --help show this help message and exit

-P, --print\_info print debug info

-S, --strip strip debug info

gxnpu\_rebuild\_ckpt:

对权重数据做量化或做float16，并重新生成ckpt 文件，用于评估模型压缩后对结果的影响

---

![](/assets/xx.jpg)

一个例子

kws\_config.yaml

PB\_FILE: kws\_i320\_norm\_cnn32\_lstm96x2\_with\_ckpt.pb

OUTPUT\_FILE: kws\_i320\_norm\_cnn32\_lstm96x2\_NCHW\_fp32\_0626.npu

SECURE: false \# true/false

NPU\_UNIT: NPU32 \# NPU16/NPU32/NPU64

COMPRESS: true \# true/false

COMPRESS\_QUANT\_BITS: 6 \# 4/5/6/7/8/9/10

FLOAT16: true \# true/false

FLOAT16\_EXP\_BITS: 5 \#3/4/5/6

OUTPUT\_TYPE: raw \# json/raw

INPUT\_OPS:

```
Feats: \[1, 1, 320\]

State\_c0: \[1, 96\]

State\_h0: \[1, 96\]

State\_c1: \[1, 96\]

State\_h1: \[1, 96\]
```

OUTPUT\_OPS: \[Model/rnn\_out, State\_c0\_out, State\_h0\_out, State\_c1\_out, State\_h1\_out\]

---

详细用例

文档：[https://github.com/NationalChip/gxDNN](https://github.com/NationalChip/gxDNN)

mnist例子：[https://github.com/NationalChip/gxDNN/tree/master/examples/mnist](https://github.com/NationalChip/gxDNN/tree/master/examples/mnist)

---

cmodel模拟器请在aliyun上进行下载

测试方式：

./main.elf /dev/npu softmax.npu

测试结果：

root@ubuntu:/home/yanfuqiang/test/cmodel/64\# ./main.elf /dev/npu softmax.npu

input\_num = 1, output\_num = 1

\[0\]: Placeholder

1 5

\[0\]: Softmax

1 5

---

使用 tensorflow 训练模型

我们以google的mnist为例子

详细可以参考：[https://github.com/NationalChip/gxDNN/tree/master/examples/mnist](https://github.com/NationalChip/gxDNN/tree/master/examples/mnist)

先把代码从git 下载下来

进入 example/mnist/compilation

确保PC上安装好了tensorflow，比如1.0

执行：python mnist.py

过程如下：

root@ubuntu:/disk2/study\_code/TensorFlow\_NPU/gxDNN-master/examples/mnist/compilation\# python mnist.py

Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.

Extracting /tmp/tensorflow/mnist/input\_data/train-images-idx3-ubyte.gz

Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.

Extracting /tmp/tensorflow/mnist/input\_data/train-labels-idx1-ubyte.gz

Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.

Extracting /tmp/tensorflow/mnist/input\_data/t10k-images-idx3-ubyte.gz

Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.

Extracting /tmp/tensorflow/mnist/input\_data/t10k-labels-idx1-ubyte.gz

W tensorflow/core/platform/cpu\_feature\_guard.cc:45\] The TensorFlow library wasn't compiled to use SSE3 instructions, but these are available on your machine and could speed up CPU computations.

W tensorflow/core/platform/cpu\_feature\_guard.cc:45\] The TensorFlow library wasn't compiled to use SSE4.1 instructions, but these are available on your machine and could speed up CPU computations.

W tensorflow/core/platform/cpu\_feature\_guard.cc:45\] The TensorFlow library wasn't compiled to use SSE4.2 instructions, but these are available on your machine and could speed up CPU computations.

W tensorflow/core/platform/cpu\_feature\_guard.cc:45\] The TensorFlow library wasn't compiled to use AVX instructions, but these are available on your machine and could speed up CPU computations.

W tensorflow/core/platform/cpu\_feature\_guard.cc:45\] The TensorFlow library wasn't compiled to use FMA instructions, but these are available on your machine and could speed up CPU computations.

0.92

接下来要把ckpt和pb文件合并成一个pb文件，执行：

python freeze\_graph.py --input\_graph=mnist.pb --input\_checkpoint=./mnist.ckpt --output\_graph=mnist\_with\_ckpt.pb --output\_node\_names=result

生成mnist\_with\_ckpt.pb文件。其中,--input\_graph后跟输入pb名，--input\_checkpoint后跟输入ckpt名，--output\_graph后跟合成的pb文件名，--output\_node\_names后跟输出结点名称，如有多个，用逗号分隔。执行完成后，在当前路径下生成mnist\_with\_ckpt.pb文件

使用gxDNN工具链编译:

gxnpuc mnist\_config.yaml

生成NPU文件mnist.npu

