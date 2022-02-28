## 基于Transformer的Rasa 3.x 内核解密之FallbackClassifier源码逐行剖析(二十五)


        本文继续围绕工业级业务对话平台和框架Rasa的Fallback Classifier的相关代码进行解析。对于一些用户输入信息，对话机器人可能会获得一个很低的classification confidence，使用Fallbacks可以帮助优雅地处理这些具有低的confidence的消息，为了处理低的NLU confidence，需要使用FallbackClassifier，这是Rasa DAG图中的一个组件。

一、关于Rasa FallbackClassifie

————————————————
版权声明：本文为CSDN博主「m0_49380401」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/m0_49380401/article/details/122612006
