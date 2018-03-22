# BML（行为描述语言）

AI Core通过BML指挥硬件做出反馈

比如：

* 报歌名，然后播放一首音乐，然后向云端取下一个行为；（这个行为往往还是：报歌名，然后播放一首音乐，然后向云端取下一个行为；只不过这是一个有明确意图的AI请求）；

* 边歌边舞（先读一段文字，调侃一下，然后开始播放音乐，并同时做一些动作）；

* 回复信息（读一段文字）；

* 执行一个行为（向前走一步，向后走一步等等）；

---

我们要设计一个BML，用来帮助用户定义硬件的行为，这个系统包括：

* 用来帮助Skill开发者描述行为的类库，它会产生BML；

* AI Service有个BML的预处理（比如当遇到BML中需要TTS的内容，它会调用云端的TTS服务，将其转换成MediaPlayer可用的链接地址，并将Speak行为置换成Play行为）；

* 本地有一个Behavior执行引擎，能解析BML，并执行它；

---

BML采用JSON格式描述，采用Behavior Tree的设计思想，是一棵单根树，它包含以下基本节点：

* SequenceBeahvior：顺序执行它的子行为；

* ParallelBeahvior：同步执行它的子行为；

* ConditionalBehavior：依据优先级寻找第一个满足条件的子行为来执行；（需要Blackboard支持）

* EmotionBehavior：执行一项表情有关的行为；（没有子节点）

* BodyBehavior：执行一项与身体有关的行为；（没有子节点）

* PlayBehavior：执行一项与播放器有关的行为；（没有子节点）

* RequestBehavior：向服务器请求一个BML，并执行之；（没有子节点）

* StopBehavior：停止Behavior的执行；（没有子节点）

---

每个行为节点，都有：循环次数（－1表示永远循环，默认不循环）、单次循环持续时间等属性

举例:

播放音乐

`{`

`"type":"sequence",`

`"behaviors":[`

`{`

`"type":"player",`

`"action":"play",`

`"url":"http://gxrobot.com/voice?id=AABBCCDDEEFFGGHHIIJJ.wav"`

`},{`

`"type":"player",`

`"action":"play",`

`"url":"http://gxrobot.com/music?id=BBCCDDEEFFGGHHIIJJKK.mp3"`

`},{`

`"type":"request",`

`"url":"http://gxrobot.com/reqest?type=next"`

`}`

`]`

`}`

---

身体动作

比如向前走两步，再返回一步

`{`

`"type":"sequence",`

`"behaviors":[`

`{`

`"type":"body",`

`"action":"step_forward",`

`"duration":1,`

`"repeat":2`

`},{`

`"type":"body",`

`"action":"step_backward",`

`"duration":1`

`},{`

`"type":"body",`

`"action":"reset"`

`}`

`]`

`}`

---

BML动作列表

已实现的动作：

```
            RESET           重置

```

眼睛类：

```
            EYE\_RED         红眼

            EYE\_GREEN       绿眼

            EYE\_BLUE        蓝眼

            EYE\_YELLOW      黄眼

            EYE\_OPEN        睁眼

            EYE\_WINK        闪烁/眨眼  

            EYE\_CLOSE       闭眼

```

头类：

```
            HEAD\_SHAKE      摇头

```

腰类：

```
            WAIST\_SHAKE     扭腰

```

手类：

```
            HAND\_GRIP       抓手/握拳

            HAND\_RELAX      松手

            HAND\_ROLL       前后手/摆手

            HAND\_PITCH      左右手/抬手

```

脚类：

```
            FOOT\_YAW        挪脚

            FOOT\_PITCH      上下脚/抬脚

            STEP\_FORWARD    往前走一步

            STEP\_BACKWARD   往后走一步

            TURN\_LEFT       往左走一步

            TURN\_RIGHT      往右走一步

               //STOP            停止（复位）

               //PAUSE           暂停

               //RESUME          恢复

```

等待\(无动作\)

{

```
"type":"delay",

"duration":2.0

```

}

所有键值

"type": 节点类型,必须有。

"name": 节点名称，可有。

"loop": 循环次数，可有

---

如果type为:selector、sequence\(串行\)、parallel\(并行\)

"successPolicy"和"failurePolicy"：检查条件

---

如果是"successPolicy"可选值为 SucceedOnOne、SucceedOnAll\(默认\);如果是"failurePolicy"可选值为FailOnAll、FailOnOne\(默认\)

"behaviors"或"children": 包含的子行为树

形式为 "behaviors":{...}

---

如果type为:delay

"duration":等待时间，单位为秒。

"return": 到时是否返回成功

---

如果type为:loop

"loop":重复次数

"successPolicy"和"failurePolicy"：检查条件

默认所有次数都成功才成功\(SucceedOnAll\),一次失败就失败\(FailOnOne\)

"behavior":{节点}

---

如果type为:player

"action":播放器动作

可选值为"play","pause","stop","resume","setVolume"

"url":播放链接

"volume":音量

---

如果type为:body

"duration":间隔时间，默认为0\(不设置\)

"action":肢体动作

