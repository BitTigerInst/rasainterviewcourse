## Rasa课程、Rasa培训、Rasa面试系列之   Rasa Version 3.x  Responses丰富的响应形式
   
Version: 3.x 
丰富的回应#
您可以通过添加视觉和交互元素来丰富响应。许多渠道支持多种类型的元素：

按钮#
以下是使用按钮的响应示例：


列表中的每个按钮 buttons 有两个键：
title：用户看到的按钮上显示的文本。
payload：点击按钮时用户发送给助手的消息。
如果您希望按钮也将实体传递给助手：

也可以通过以下方式传递多个实体：
'/intent_name{{"entity_type_1":"entity_value_1", "entity_type_2": "entity_value_2"}}'

用按钮绕过 NLU，您可以使用按钮绕过 NLU 预测并触发特定意图和实体。以 /开头的消息 直接发送到 RegexInterpreter，它需要 NLU 输入的/intent{entities}格式。在上面的例子中，如果用户点击一个按钮，用户输入将被直接归类为要么mood_great或mood_sad意图。
RegexInterpreter您可以使用以下格式包含意图传递给 的实体：
/inform{"ORG":"Rasa", "GPE":"Germany"}   ，将RegexInterpreter根据意图对上面的消息进行分类，并分别inform提取 类型和类型的实体：Rasa Germany  ORG  GPE
 
在 DOMAIN.YML 中转义花括号：
您需要/intent{entities}在 domain.yml 中使用双花括号编写响应，以便助手不会将其视为响应中的变量并在花括号内插入内容。

检查您的频道
请记住，如何显示定义的按钮取决于输出通道的实现。例如，某些频道对您可以提供的按钮数量有限制。查看频道连接器下的频道文档，了解任何特定于频道的限制。


图片#
您可以通过在键下提供图像的 URL 来将图像添加到响应中image：


自定义输出负载#
custom您可以使用该键将任意输出发送到输出通道 。输出通道接收存储在custom密钥下的对象作为 JSON 有效负载。

这是一个如何将 日期选择器发送到 Slack 输出通道的示例：




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




Gavin导师
星空智能对话机器人创始人/AI双线思考法作者
