## Rasa课程、Rasa培训、Rasa面试系列之：Default Actions action_listen 、action_restart 、action_session_start
 
Default Actions
Default Actions是默认内置到对话管理器中的操作。其中大部分是根据某些对话情况自动预测的。您可能需要自定义这些以个性化您的助手。

这些操作中的每一个都有一个默认行为，在下面的部分中进行了描述。为了覆盖此默认行为，请编写一个自定义操作 ，其name()方法返回与默认操作相同的名称：


将此操作添加到域文件的操作部分，以便您的助手知道使用自定义定义而不是默认定义：


将此操作添加到您的域文件后，使用 rasa train --force. 否则 Rasa 不会知道你已经改变了任何东西，并且可能会跳过重新训练你的对话模型。

action_listen #
预计此操作将发出信号，即助手不应执行任何操作并等待下一个用户输入。

action_restart #
此操作会重置整个对话历史记录，包括在此期间设置的任何插槽。

如果RulePolicy包含在模型配置中，它可以由用户在对话中通过发送“/restart”消息来触发。如果您utter_restart在域中定义响应，则该响应也会发送给用户。

action_session_start #
此操作启动一个新的会话 ，并在以下情况下执行：

在每次新对话开始时
session_expiration_time用户在域 会话配置中的参数定义的一段时间内处于非活动状态后
当用户在对话期间发送“/session_start”消息时
该操作将重置对话跟踪器，但默认情况下不会清除任何已设置的插槽。

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
