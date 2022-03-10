## Rasa对话机器人项目实战之教育领域Education Bot项目Session自定义、Rich Response解密及案例剖析（七十三)

本文继续围绕工业级业务对话平台和框架Rasa，对Rasa对话机器人项目实战之教育领域Education Bot项目Session自定义、如何覆写action_session_start来实现自定义的功能，关于Rich Response的使用等结合案例进行详细分析。

 一、Rasa对话机器人项目实战之教育领域Education Bot项目Session自定义、Rich Response解密及案例剖析

    Domain中的config运行机制剖析及配置实践

这是domain中的config信息，这些是对话机器人在系统和session层面上使用的配置信息：

Store_entites_as_slots这里设为true，表示把从用户消息提取的entities保存到当前session的slots中。当一个entity和”from_entity” slot mapping匹配时，如果这个参数设置为true，则entity的值会被设置到对应的slot中，如果在story中手动添加了slot_was_set，则会被跳过。可以把参数设置为false来关闭这项功能，样例如下：

 2.  session_expiration_time、carry_over_slots_to_new_session设置最佳实践

-session_expiration_time指定session超时的时间，超时之后需要启动新的session才能执行对话。

对话session可以用以下三种方式开始：

-用户和对话机器人开始对话

-在session超时之后用户发送第一条消息给对话机器人

-使用intent “/session_start”来触发新的session

                                                                                                       

-carry_over_slots_to_new_session指定是否之前session中已有的slots需要被带入到新的session中。当设置为true则之前session中已有的slots会被带入新session。

 3.  action_session_start运行机制及最佳实践

一个session启动时会触发默认action “action_session_start”，默认实现是把当前所有存在的slots带入到新的session中。注意所有对话都是以action_session_start开始，覆写这个action可以使用通过外部API调用获取的slots来初始化tracker，或者使用一条对话机器人消息来启动对话。如果你不想让所有的slots都带入新的session，而仅仅只带入用户名字和电话号码，你可以使用以下自定义action来覆写默认的action_session_start：

如果在上面的fetch_slots方法中需要调用外部第三方的API来获取slots信息时，需要添加一个__init__方法来初始化数据库连接等内容，而初始化方法会在action server加载action时执行，这样在调用微服务时就可以直接使用了。

当用户消息触发了session start时，你为了获取用户消息相关的metadata信息，可以访问这个特殊的slot “session_started_metadata”，以下是示例：

另外一个样例是，当用户刚登录对话系统时，如果能够获取用户的地理位置或者偏好信息，那么一旦用户开始与对话机器人对话时我们就可以使用这些信息来设置slots，举个例子，如果用户来自NYC，当用户问对话机器人天气情况时，由于对话机器人已经通过metadata拿到用户位置信息就可以直接查询NYC的天气并提供给用户，而不用询问用户的位置信息。

 4.  Using Variables in Responses解析与示例

Responses也是一种action，只不过这种action是直接发送消息给用户，而不需要运行任何自定义代码或者返回事件。Responses是直接定义在domain文件中的responses key下面，可以包括富文本内容如button和attachment。你可以使用变量来向responses中写入信息，譬如下面的例子中，Rasa自动使用slot “name”中的值来替换responses中的占位符{name}：

 5.  Channel-Specific Response Variations解析与示例

为了根据当前用户使用哪一个channel来让对话机器人选择不同的responses，可以使用channel key来指定与特定channel对应的response，譬如下面的例子中，第一个response是只用于channel “slack”的，而第二个response的使用与channel无关：

使用时需要注意确保channel key的值需要与你的输入channel的name方法返回的值一致。如果你使用一个built-in channel，这个key的值也需要与你的credentials.yml文件中的channel名称一致。

 6.  Conditional Response Variations解析与示例

可以基于slot的值使用条件判断来选择response，这种response与普通的response相比在于定义时需要指定condition key，这个key指定了包含slot name和value的列表。当对话触发一个response时，会根据当前对话状态slot的值来检查有哪一个response是匹配当前条件的。需要注意对话状态slot值和domain中response定义使用的slot限定值是使用符号==来匹配的，即不仅值要相同，slot类型也要一样（例如boolean类型的true和string “true”是不一样的）。在下面的例子中，slot “logged_in”设置为true：

 7.  Rich Responses解析与示例

Rich responses的使用包括button的使用或者image的使用等等。关于button的使用的好处包括方便用户进行操作，并可以根据button的payload来直接发送用户intent，而不需要经过NLU的预测，示例如下：

也可以通过button来传递entities给对话机器人：

下面是传递多个entities的示例：

'/intent_name{{"entity_type_1":"entity_value_1", "entity_type_2": "entity_value_2"}}'

需要注意的是在上面的例子中需要使用两个大括号来包含entities部分的内容。
————————————————
版权声明：本文为CSDN博主「m0_49380401」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/m0_49380401/article/details/123390317
