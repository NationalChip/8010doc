# 开发一个简单的技能

下面，我们来一起实现一个简单的demo，来熟悉本地离线技能的开发。

---

自己建立一个skill 模块

因为代码会遍历ai\src\skills 目录下所有的目录，所以新建skill 必须在该目录下

必须按照规定的格式

实现arithmetic skill

![](/assets/6a072834-3bc6-11e8-8ff5-8c89a5b77afc.bmp)

内部至少包含：Vocab，\_\_init\_\_.py，skill.json

![](/assets/skill%E5%BC%80%E5%8F%91%E7%AE%80%E5%8D%95%E5%85%A5%E6%89%8B2.bmp)

skill.json 内容如下：

{

"name":"arithmetic",

"active":"on",

"usr\_intents":\[

{

"name":"EasyArithmeticIntent",

"handler":"handle\_easy\_arithmetic\_intent",

"require":\["EasyArithmeticKeyword"\],

"optional":\[\],

"one\_of":\[\]

}

\]

}

\_\_init\_\_.py 内容如下：

\#coding=utf-8

\_\_author\_\_ = 'gx'

def handle\_easy\_arithmetic\_intent\(message, session, config, dialog\):

print "yanfuqang test"

return None

if \_\_name\_\_ == "\_\_main\_\_":

pass

在EasyArithmeticKeyword.voc 实现好自己的关键字，就会执行我们实现的skill 函数了：handle\_easy\_arithmetic\_intent

