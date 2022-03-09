## Rasa课程、Rasa培训、Rasa面试系列之： Rasa Version 3.x Rasa微服务高级用法-动态表单

Version: 3.x      
 
动态表单行为#
默认情况下，Rasa Open Source 将在域文件中为您的表单列出的插槽中请求下一个空插槽。如果您使用 自定义插槽映射和FormValidationAction，它将要求该required_slots方法返回的第一个空插槽。如果填写了required_slots的所有插槽，则表单已经填好了，表单将被停用。

如果需要，您可以动态更新表单的所需插槽。例如，当您需要根据前一个插槽的填充方式获得更多详细信息或您想要更改请求插槽的顺序时，这很有用。

如果您使用的是 Rasa SDK，我们建议您使用FormValidationAction和覆盖required_slots以适应您的动态行为。通过extract_<slot name>为每个不使用预定义映射的插槽实现一个方法，如自定义插槽映射中所述。下面的示例将询问用户就餐是否想坐在阴凉处或阳光下，当他们想坐在外面时。


相反，如果您想required_slots在特定条件下从域文件中定义的表单中删除一个插槽，则应复制domain_slots到新变量并将更改应用于该新变量，而不是直接修改 domain_slots. 直接修改domain_slots可能会导致意外行为。例如：



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
