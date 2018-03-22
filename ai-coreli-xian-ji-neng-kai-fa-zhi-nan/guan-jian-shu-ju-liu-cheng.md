# 关键数据流程

通过socket传输，agent 服务接收senseflow的数据，然后经过个 service 服务处理，再走到 skills 服务

独立剥离出agent，是为了可以支持本地和云端的操作

由agent决定走本地离线处理还是云端处理

---

在本地离线处理中，数据最后走到skills服务的：handle\_utterance，然后使用adapt 进行关键字匹配，寻找合适的intent。

