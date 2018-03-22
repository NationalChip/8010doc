# skill基础

一个skill相当于一个应用，由一个或多个intent构成， 一个intent对应着一个具体的目标行为。当输入语句的NLP结果与某个intent匹配时，该intent对应的handler函数将被调用，进行相应的处理并做出反馈。

intent分为系统intent（sys\_intents）和自定义intent（usr\_intents）。

* sys\_intents：系统预置的intent，skill可以订阅并实现自己的handler，目前可用的系统intent有：

* sys\_other\_intent：默认intent，当无法匹配到合适的intent时，会产生该intent；

---

usr\_intents：根据需求提供关键词来自定义的intent，关键词可以是一些固定的词句或者正则表达式，保存在vocab和regex目录下对应名称的文件中；

一个intent对关键词的依赖有三种情况：

* require：必需的；

* optional：可选的；

* one\_of：多选一；NLP引擎会根据提供的关键词句对输入语句进行解析，来判断是否命中intent。

---

目前skill需放置在ai\_framework/ai/src/skills下，一个典型的skill目录结构如下：

sample\_skill

* skill.json

* \_ init \_.py

* vocab

* \[ \] Keyword1.voc

* \[ \] Keyword2.voc

* regex

* \[ \] Keyregex1.rx

* \[ \] Keyregex2.rx

* dialog

* \[ \] reply1.dialog

* \[ \] reply2.dialog

---

skill.json：

skill的描述文件，定义了skill的名字，包含的intent，intent的handler和依赖的关键词组。一个简单的例子：

`{`

`"name":"weather_demo",`

`"active":"on",`

`"usr_intents":[`

`{`

`"name":"WeatherDemoIntent",`

`"handler":"handle_weather_intent",`

`"require":["AskKeyword", "WeatherKeyword"],`

`"optional":["DayKeyword"]`

`}`

`]`

`}`

weather\_demo skill，无系统预置intent，有一个自定义的WeatherDemoIntent，处理函数是handle\_weather\_intent，该intent依赖三类关键词组：AskKeyword， WeatherKeyword， DayKeyword， 其中AskKeyword和WeatherKeyword是必需的，DayKeyword是可有可无的。

---

\_ init \_.py：

这里实现了skill.json中描述的intent handler函数，当产生特定的intent时，其handler函数会被调用；

参数message包含了intent信息，特别是message.metadata字典中包含了自定义关键词参数；

dialog用来调用回复模板；

intent handler返回behavior字典，参考这里

在上面的例子中，当匹配到WeatherDemoIntent时，handle\_weather\_intent将被调用，并返回一个speak behavior。

`def gen_speak_action(speak_str):`

`action = {}`

`action['type'] = 'sequence'`

`action['behaviors'] = []`

`behavior = {'type':'speak', 'content':speak_str}`

`action['behaviors'].append(behavior)`

`return action`



`def handle_weather_intent(message, session, config, dialog):`

`weather = message.metadata.get('WeatherKeyword')`

`day = message.metadata.get('DayKeyword', u'今天')`

`print day + weather`

`return gen_speak_action(day + weather)`

---

vocab：

该目录下存放了关键词文件，关键词用于构建intent，例如weather skill的vocab下有这些文件及内容： •WeatherKeyword.voc：天气， 气象， 天象

AskKeyword.voc：怎么样， 如何， 怎样， 还好吗

DayKeyword.voc：今天， 明天

根据上面weather skill.json的描述，当输入语句同时含有WeatherKeyword和AskKeyword中的关键词时就会匹配到该intent，DayKeyword是可选的，如果存在将提供具体哪一天的信息。

---

regex：

该目录下存放了正则表达式文件，用于构建intent，正则表达式可对输入的字符串按需求进行匹配，以phone\_demo skill为例： •phone.rx：给 \(?P.\*\)

定义了一个模式“Name”，即紧跟“给”字，除去换行之外的任意字符组合（.指任意字符，\*代表可重复的）；

将该模式加入skill.json列为require项：

`{`

`"name":"phone_demo",`

`"usr_intents":[`

`{`

`"name":"PhoneDemoIntent",`

`"handler":"handle_phone_intent",`

`"require":["PhoneKeyword", "Name"]`

`}`

`]`

`}`

对于存在符合该模式的字符串，对应的子串将会通过message.metadata传给handler函数，例如:

`def handle_phone_intent(message, session, config, dialog):`

`name = message.metadata.get('Name')`

`action = {'voice':u'喂,' + name}`

`return action`

输入：打电话给老司机

输出：喂， 老司机

输入：给萝卜头打电话

输出：喂，萝卜头

---

dialog：

该目录下存放了回复语句的模板文件，模板描述了某类回复的集合，通过dialog对象使用，用来提高回复的灵活性；

例如helloworld skill的dialog下有以下文件及内容：

thank.you.reply.dialog：不客气， 不用谢， 您太客气了， 么么哒

hello.reply.dialog：你好 ， 很高兴见到你

---

对应了两个回复模板thank.you.reply和hello.reply，使用方法：

dialog.render\("thank.you.reply"\)

将随机返回“不客气， 不用谢， 您太客气了， 么么哒”中的一个字符串

---

hello.reply展示了带参数的例子：

dialog.render\('hello.reply', {'user':u'老司机'}\)

参数以字典方式组织，传递到模板替换包含的与key同名的参数，得到的结果就是 “你好 老司机” 或者 “老司机 很高兴见到你”

