# SenseFlow运行机制

**如何进行元件的注册**

见目录：senseflow\components

组件实现好以后，需要注册进 sensflow，使用如下函数：

![](/assets/GX8010_SenseFlow%E5%BC%80%E5%8F%91%E6%8C%87%E5%8D%97.jpg)

---

随便找个例子，比如：sf\_delay\_buffer.cc （SfDelayBuffer）

我们可以看到，在文件的最后，调用了该函数

![](/assets/2.bmp)

---

运行机制

要了解整个 sensflow 的机制，就先需要了解：class SfComponent

组件是什么？

![](/assets/3.jpg)

我们实现的任何一个组件必须从SfComponent 继承而来。

这样，该组件就可以加入到SfCircuit 中，并且可以和其它的组件连接。

SfCircuit 就类似下面的这个东东，组件在内部进行了连接，数据按照连接的方向流动，

![](/assets/4.jpg)

每个组件需要有 输入 接口和 输出 接口，并且内部要实现process 处理函数

就像下面这个组件，有一个输入接口和一个输出接口

![](/assets/5.jpg)

组件的操作是“拉”模式

自己实现组件的时候，必须实现如下两个接口：

virtual void Process\_\(SfSignalBus&, SfSignalBus&\);

virtual bool ParameterUpdating\_\(int, SfParameter const&\);

每当 input 端口进来一包数据后，sensdflow 会调用该模块的Process\_ 函数，对数据进行处理，然后，把处理过的数据输出到output 端口上，提供给下一个模块，作为它的输入数据

注意下面两个接口：

void Tick\(\);

void Reset\(\);

众多的组件能够进行数据流的处理，是由于一个主线程或者另外的线程不断调用

Tick\(\)，Reset\(\) 来进行的。

![](/assets/6.jpg)从上图，我们就可以看出，为什么叫做 “拉模式”

因为数据的流动处理，都是从最后一个组件进行拉操作，一直遍历input 连接上的上一个组件，一直遍历下去至到最后一个。

class SfSignal：

组件处理的数据称为 "signals"，"signals" 包装在class SfSignal 中。组件处理的数据都是一个一的SfSignal 对象+

class SfSignalBus：

是SfSignal 的容器，每个input 或者output 就是一个SfSignalBus

