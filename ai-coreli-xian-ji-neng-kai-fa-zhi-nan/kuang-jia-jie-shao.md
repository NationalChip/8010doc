# 框架介绍

AI Framework实现对来自robot用户的自然语言信息做出处理及反应，能够对文本化的信息进行NLP处理得到intent，激活相关的skill handler来完成预定义的行为，并根据具体情况返回action。

基于云端一体化的思想设计实现，在终端和云端都可部署，选择python语言进行开发，以提高软件的复用度，降低开发及维护成本。

框架定义了skill的形态及开发模式。

---

主要组件和模块

* configurationmgr： 系统配置管理

* messagebus： 负责框架内各模块间的消息派送，基于tonardo server及websocket client进行通信

* agent： 与外部交互的接口，用unix domain socket实现，数据和参数则用json包装

* skillcore： 负责添加，更新，删除skill，绑定/解除skill语言素材，intent和handler的对应关系，加载skill默认配置

* adapte： 根据语言素材，理解输入的文本信息得到合适的intent

* skillactivity： 激活intent所对应的skill，构造session等运行环境

* sessionmgr： 维护skill活动所需的环境变量和上下文状态，session信息可能保存在内存，文本文件，redis或者db中

* skilldialog:：根据语言素材，得到skill具体的应答语句



