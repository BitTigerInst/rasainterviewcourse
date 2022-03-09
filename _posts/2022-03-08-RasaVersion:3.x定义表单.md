## Rasa课程、Rasa培训、Rasa面试系列之 Rasa Version 3.x   定义表单

Version: 3.x  
表单
最常见的对话模式之一是从用户那里收集一些信息以便做某事（预订餐厅、调用 API、搜索数据库等）。这也称为**槽位填充**。

用法#
要在 Rasa Open Source 中使用表单，您需要确保将 规则策略添加到您的策略配置中。例如：


定义表单#
通过将表单添加到域中forms的部分来定义表单。表单的名称也是您可以在 故事或规则中用于处理表单执行的操作的名称。您需要为强制键指定一个槽位名称列表 required_slots，以下示例表单restaurant_form将填充 slot cuisine和 slot num_people。


ignored_intents您可以为键下的整个表单定义要忽略的意图列表 ，列出的意图ignored_intents将添加not_intent到每个插槽映射的键中。例如，如果您不希望在意图为chitchat 时填写表单的任何必需的插槽 ，则您需要定义以下内容（在表单名称之后和ignored_intents关键字下）：

一旦表单动作第一次被调用，表单就会被激活并提示用户输入下一个所需的槽值。它通过查找调用 的响应utter_ask_<form_name>_<slot_name>或 utter_ask_<slot_name>找到前者来做到这一点。确保在您的域文件中为每个必需的插槽定义这些响应。
  


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
