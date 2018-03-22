# 开发自己的元件

接下来，我们先尝试写两个简单的组件SfYFQSource 和SfYFQOutput

文件为：sf\_yfq\_output.cc，sf\_yfq\_source.cc

SfYFQSource 是数据源，所以没有input 端口，只有一个output 端口，它不断的产生数据"hello world" 给到下一个组件。

SfYFQOutput 是最后把数据展示的模块，所以没有output，只有一个input，它把接收到的数据不断的在屏幕上打印出来。

---

class SfYFQSource : public SfComponent

{

public:

SfYFQSource\(\)

{

AddOutput\_\("output\_yfq"\);

}

~SfYFQSource\(\)

国芯科技内部资料

第 12 页 共 15 页

{

}

protected:

virtual void Process\_\(SfSignalBus& inputs, SfSignalBus& outputs\)

{

std::string str = "hello world";

outputs.SetValue\("output\_yfq", str\);

}

virtual bool ParameterUpdating\_\(int index, SfParameter const& param\)

{

return true;

}

};

REGISTER\_COMPONENT\(SfYFQSource\)

我们进行编译，执行，发现SfYFQSource 已经成功加入了senseflow 框架

\#include &lt;senseflow.h&gt;

class SfYFQOutput : public SfComponent

{

public:

SfYFQOutput\(\)

{

AddInput\_\("input\_yfq"\);

}

~SfYFQOutput\(\)

{

}

protected:

virtual void Process\_\(SfSignalBus& inputs, SfSignalBus& outputs\)

{

std::string str;

if \(!inputs.GetValue\("input\_yfq", str\)\)

{

printf\("get str error\n"\);

return;

}

printf\("str = %s\n", \(char\*\)str.c\_str\(\)\);

}

virtual bool ParameterUpdating\_\(int index, SfParameter const& param\)

{

return true;

}

};

国芯科技内部资料

第 13 页 共 15 页

REGISTER\_COMPONENT\(SfYFQOutput\)

我们建立一个yfq\_test.sf, 把组件连接起来，内容如下：

{

"name":"YFQ\_TEST",

"threadCount":1,

"components":\[

{

"name":"yfq\_source",

"class":"SfYFQSource",

"properties":{

}

},

{

"name":"yfq\_output",

"class":"SfYFQOutput",

"properties":{

}

}

\],

"wires":\[

{

"from":{"component":"yfq\_source","port":"output\_yfq"},

"to":{"component":"yfq\_output","port":"input\_yfq"}

}

\]

}

然后运行程序，就可以看到屏幕不断的打印：+

Hello world 了

