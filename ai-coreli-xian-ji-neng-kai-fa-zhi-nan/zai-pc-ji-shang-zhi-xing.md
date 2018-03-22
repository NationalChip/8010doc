# 在PC机上执行

该框架是python语言开发的，需要开发者有基本的python基础

Linux PC机上请安装好python2.7

---

运行方式如下：

进入代码的ai 目录下，启动下面三个服务

* ./launcher.sh service
* ./launcher.sh skills
* ./launcher.sh agent

---

然后我们开启一个测试程序：

目录在：**/ai/src/client/extern**

执行：yanfuqiang@ubuntu:~/bux/ai/src/client/extern$ python ext\_test.py

我们在测试程序中，输入文字进行测试

这里的“文字”在整个流程中，应该是我们的语音通过TTS转行得到的文本文字。为了方便测试，在这里，我们直接使用“文字”进行测试

---

演示效果：

root@ubuntu:/disk2/BUX\_Master/code/ai/src/client/extern\# python ext\_test.py

Input:

放一首张学友的歌

`{u'action': {u'behaviors': [{u'behaviors': [{u'action': u'play', u'content': u'\u597d\u7684', u'type': u'player', u'url': u'file:///media/tts/11.mp3'}, {u'action': u'play', u'content': u'wenbie.mp3', u'type': u'player', u'url': u'file:///media/music/wenbie.mp3'}], u'type': u'sequence'}, {u'behaviors': [{u'type': u'loop', u'loop': -1, u'behavior': {u'behaviors': [{u'action': u'EYE_YELLOW', u'duration': 1, u'type': u'body'}, {u'action': u'EYE_ORANGE', u'duration': 1, u'type': u'body'}, {u'action': u'EYE_RED', u'duration': 1, u'type': u'body'}, {u'action': u'EYE_PINK', u'duration': 1, u'type': u'body'}, {u'action': u'EYE_PURPLE', u'duration': 1, u'type': u'body'}, {u'action': u'EYE_PURPLE_1', u'duration': 1, u'type': u'body'}, {u'action': u'EYE_BLUE', u'duration': 1, u'type': u'body'}, {u'action': u'EYE_CYAN', u'duration': 1, u'type': u'body'}, {u'action': u'EYE_GREEN', u'duration': 1, u'type': u'body'}, {u'action': u'EYE_LIGHT_GREEN', u'duration': 1, u'type': u'body'}], u'type': u'sequence'}}]}], u'successPolicy': u'SucceedOnOne', u'type': u'parallel'}, u'ip': u'127.0.0.1', u'req_time': u'20180108101105', u'name': u'music_demo', u'suspendable': u'true'}`

这个我们就拿到了数据，该数据反馈给senserflow进行处理，把u'file:///media/tts/11.mp3 指定的声音进行播放

