# 概述

GXDNN用于将用户生成的Tensorflow模型编译成可以被NPU硬件模块执行的指令，并提供了一套API让用户方便地运行Tensorflow模型。![](/assets/32bbd801cb5134b0cdbb685b54c0cce6?showdoc=.jpg) 图：gxDNN神经网络处理器的作用

GXDNN包括NPU编译工具和NPU执行器。NPU编译工具用于生成能够被NPU执行器执行的文件，该文件里包含了NPU执行指令和模型的描述信息。编译器可以在X86或ARM的Linux环境下使用。

NPU执行器提供了一套API，用于解析执行文件，加载模型，输入数据，运行模型得到输出结果。它需要在有NPU硬件模块的机器上使用。

# 使用GXDNN步骤

1. 在PC上生成Tensorflow的Graph\(pb文件\)和Variable（ckpt文件）后，并将两者合并为一个pb文件。

2. 在PC上使用GXDNN的NPU编译器，将pb文件转化成能被NPU加载执行的指令文件。

3. 芯片端调用NPU加载函数，把JSON文件传给加载器，建立模型。

4. 芯片端调用NPU执行函数，传入输入数据，运行得到输出数据。

![](/assets/9cbd3d61d2e48d9d5d57cc6f97fce42b?showdoc=.jpg)

# NPU编译工具

NPU硬件模块用于加速神经网络的运算。提供了乘加、卷积、激活函数、降采样等功能。为了匹配NPU硬件模块，需要将基于tensorflow的模型拆分成一个个小的执行单元，再分别使用硬件模块计算。

Tensorflow的运算流程基于图（graph），图的结点称为op，op将0个或多个输入数据进行计算，生成0个或多个输出数据。NPU编译器的主要工作就是把一个个op转变成可以被NPU硬件模块执行的命令。

# NPU执行器

1. 用户使用NPU编译工具生成NPU文件后，调用NPU执行器提供的API接口，完成如下步骤：

2. 导入模型

3. 获取该模型的输入输出OP的信息

4. 填充输入数据

5. 运行

6. 得到输出数据。



