## 基于Transformer的Rasa 3.x 内核解密之业务逻辑Action Servers架构设计与核心运行流程(二十六)


        本文继续围绕工业级业务对话平台和框架Rasa的业务逻辑Action Servers架构设计与核心运行流程进行解析。Rasa action sever是对微服务的封装，即Rasa提供了一套API来封装我们已有的功能或者可以根据API开发业务逻辑来和Rasa server进行交互，譬如在订机票或订酒店的场景，用户通过和Rasa对话系统进行交互来调用其背后的微服务。

一、Rasa对话机器人业务逻辑Action Servers架构设计与核心运行流程解析


————————————————
版权声明：本文为CSDN博主「m0_49380401」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/m0_49380401/article/details/122631601
