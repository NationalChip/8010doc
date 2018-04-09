# SenseFlow基本介绍

**代码入口**

整个应用的入口 main 函数在：

senseflow\daemon\daemon.cc

---

**Senseflow 说明**

Senseflow，我们称为信号处理系统

它负责语音的拾取，前处理，特征提取，调用GXDNN处理数据，合成语音，分词，语音播放，与云端交互等功能。

SenseFlow使用C/C++开发，全部采用模块和参数配置的方式工作，客户不需要编写任何C/C++代码即可实现系统配置

---

**SenseFlow的基本概念：**

1 元件（SfComponent）

元件是最基本的信号处理单元，元件包含“参数SfParameter”、“输入总线SfWireBus”、“输出总线SfWireBus”和”处理SfComponent::Process\(\)“几个元素

•参数SfParameter：元件的设定值，可以是整形、浮点型、布尔值、字符串、列表等；

•输入总线SfSignalBus：元件的输入信号汇集的地方，每个信号有自己的Port；

•输出总线SfSignalBus：元件的输出信号汇集的地方，每个信号有自己的Port；

•处理SfComponent::Process\(\)：元件的信号处理过程；

---

2 连线（SfWire）

元件和元件之间通过“连线SfWire“连接在一起，连线代表一种元件之间的依赖关系。

SfWire是有向的，指明了源（元件/Port）和目标（元件/Port）。

每个元件包含一个连线集（SfWireBus），指明了元件信号的来源。

一个元件的输出Port可以连接到多个元件的多个输入Port，但是输入Port只能连接到一个输出Port

---

3 信号，总线（SfSignalBus）

元件的输入输出信号存放的位置，它可以承载各种类型各种尺寸的数据SfSignal，比如std::vector&lt;T&gt;、float等等。

元件的输出总线的数据的内存由本元件管理，输入总线的数据的内存由源管理。

---

4 回路（SfCircuit）

回路是一组连接在一起的元件，它是元件的工作环境。

回路也是一种元件，它可以被添加到另一个回路中，与其他元件连接在一起。

回路也有自己的输入总线和输出总线，并可以和回路内的元件连接。

SenseFlow存在一个顶层回路，用来驱动那些需要自驱动的元件

---

5 线程（SfThread）

SenseFlow采用一种线程池的机制来执行信号处理，用户可以设定线程的数量，如果设为0，则需要用户自行驱动回路的运行

---

6 插件（SfPlugin）

SenseFlow也支持插件，插件是一个动态链接库，可以通过插件加载元件

---

元件列表

这里是一份元件列表

•SfGain：对信号乘以一个系数；

•SfAdder：求两个信号的和；

•SfAudioDevice：音频设备的输入和输出（支持Ubuntu Linux和MacOSX）

•SfWaveStreamer：读取Wav文件；

•SfSpeexDsp：使用SpeexDSP库实现的降噪、回声消除（尚不支持）、VAD、去混响等功能；

•SfJieba：使用cppjieba实现的分词功能；

•SfSptkFeature：使用SPTK实现的音频特征提取；

•SfSptkVocoder：使用SPTK实现的音频合成器；

•SfFIRFilter：FIR滤波器；

•SfGxDnn模块组：使用GXDNN实现的神经网络处理元件的基类（在PC上可以使用CModel模拟）可以派生出

•SfGxDnnAsr：语音识别元件；

•SfGxDnnKws：关键词识别元件；

•SfGxDnnTts：语音输出元件；

•SfGxAIAgentCloud：与云端AI框架交互的元件（将经过处理的语音数据发送到云端，并接收服务器的事件）

•SfGxAIAgentLocal：与本地AI框架交互的元件（将ASR识别的文本数据发送给本地AI框架，并接收AI框架返回的事件）

•SfDecisionMaker：一个简单的决策执行器；

•SfRobotBehavior：执行机器人行为的脚本语言，比如眨眼、说话、运动等；

•SfUserInput：用户按钮的输入元件；

•SfSystemEvent：系统事件的输入元件；

•SfMux：将各种信号混合的元件；

以下模块均需要X10的硬件支持

•SfGxAudioInput：X10的音频设备的输入，主要是因为VAD、BeamForming等算法被放置在DSP了，音频输入将提供更多的内容；

•SfGxAudioOutput：X10的音频设备的输出

•SfGxMusicPlayer：利用X10硬件加速的音乐播放；

---

Sf 配置

用户可以使用JSON语言来配置回路，包括：

•元件的个数，类名，名称和各项属性；

•元件之间的连接关系；

具体实例可在/usr/share/senseflow/sf 目录下找到

以下是个简单的回路图，它可以将麦克风和wave的信号叠加在一起并输出：

{

"name":"SenseFlow @ MacOSX",

"threadCount":3,

"components":\[

{

"name":"input",

"class":"SfAudioDevice",

"properties":{

"DeviceList": 2,

"IsStreaming":false,

"SampleRate":44100

}

},

{

"na":"wave",

"cl":"SfWaveStreamer",

"pr":{

"FilePath":"/Users/alan/Reference/DSPatch/example/Sample.wav",

"Play":true

}

},

{

"na":"adder",

"cl":"SfAdder"

},

{

"name":"output",

"class":"SfAudioDevice",

"properties":{

"IsStreaming":false,

"SampleRate":44100

}

}

\],

"wires":\[

{

"from":{"component":"wave","port":"SampleRate"},

"to":{"component":"output","port":"SampleRate"}

},

{

"from":{"component":"wave","port":0},

"to":{"component":"adder","port":0}

},

{

"from":{"component":"input","port":0},

"to":{"component":"adder","port":1}

},

{

"from":{"component":"adder","port":0},

"to":{"component":"output","port":0}

},

{

"from":{"component":"adder","port":0},

"to":{"component":"output","port":1}

}

\]

}

---

可视化

GraphEditor 最初的目的，是方便查看SenseFlow 目录下的circuits 内，后缀为sf 的文件

由于sf文件描述了元件的连接状态，直接看sf 文件基本很难看的清晰，不如可视图来的方 便

因此，开发了这么一个辅助工具，可以查看，编辑 sf 文件，可以创建新的sf 文件

![](/assets/1.jpg)

[    
](https://13421398942.gitbooks.io/gx8010_senseflow/content/di-3-zhang-senseflow-yun-xing-ji-zhi.html)

