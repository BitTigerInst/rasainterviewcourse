## Rasa课程、Rasa培训、Rasa面试系列之 Rasa Version: 3.x  Responses使用变量
   
Version: 3.x 
回应
响应是您的助手发送给用户的消息。响应通常只有文本，但也可以包括图像和按钮等内容。

定义响应#
响应responses位于您的域文件或单独的“responses.yml”文件中的键下。每个响应名称应以utter_. 例如，您可以在响应名称下添加问候和说再见的响应utter_greet，并且utter_bye：

如果您在助手中使用检索意图，您还需要为助手对这些意图的回复添加响应

笔记
注意检索意图的响应名称的特殊格式。每个名称都以 开头utter_，然后是检索意图的名称（此处chitchat），接着是指定不同响应键的后缀（此处ask_name和ask_weather）。请参阅NLU 训练示例的文档以了解更多信息。
在响应中使用变量#
您可以使用变量将信息插入响应中。在响应中，变量用大括号括起来。例如，请参见name下面的变量：

使用utter_greet响应时，Rasa 会自动使用在名为name的槽中找到的值填充变量 。如果这样的槽位不存在或为空，则变量将填充为None.

填充变量的另一种方法是在自定义操作中。在您的自定义操作代码中，您可以为响应提供值以填充特定变量。如果您将 Rasa SDK 用于您的操作服务器，您可以将变量的值作为关键字参数传递给dispatcher.utter_message：

如果您使用不同的自定义action server，请通过向服务器返回的响应添加额外参数来提供值：

Rasa官网链接： https://rasa.com/ 

Gavin大咖课程信息分享：
NLP on Transformers高手之路137课（模型、算法、论文、源码、案例 + 1年答疑）
Rasa 3.x 源码高手之路：系统架构、内核算法、源码实现详解



Gavin大咖简介
星空智能对话机器人创始人、AI通用双线思考法作者，现工作于硅谷顶级的AI实验室。专精于Conversational AI. 在美国曾先后工作于硅谷最顶级的机器学习和人工智能实验室 
Gavin大咖微信：NLP_Matrix_Space  
联系电话：+1 650-603-1290
联系邮箱：hiheartfirst@gmail.com
助教老师微信：Spark_AI_NLP  
