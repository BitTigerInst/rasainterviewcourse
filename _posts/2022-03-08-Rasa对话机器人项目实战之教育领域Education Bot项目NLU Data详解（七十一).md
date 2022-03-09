## Rasa对话机器人项目实战之教育领域Education Bot项目NLU Data详解（七十一)
本文继续围绕工业级业务对话平台和框架Rasa，对Rasa对话机器人项目实战之教育领域Education Bot项目NLU Data的分层结构，数据格式，在意图分类和实体提取时如何使用正则表达式以及lookup table，在提取实体时如何使用role和group标签等内容进行详细解析。

 一、Rasa对话机器人项目实战之教育领域Education Bot项目NLU Data详解

    Education Bot项目NLU Data架构设计High-Level Structure四大核心解析

NLU的目标是从用户输入中提取结构化的信息，通常包括intent和entities。可以通过添加正则表达式和lookup tables到NLU训练数据里来帮助更好地识别intents和提取entities。

每个YAML文件可以包括多个keys所对应的训练数据，但是在一个文件中，一个key只能出现一次。可用的key有4类：

version：应该在所有文件中都指定，否则Rasa会假设使用当前安装版本所支持的最新数据格式

nlu：指定NLU训练数据

stories：用于训练机器学习模型识别当前对话训练数据所使用的模式，从而泛化到当前不可见的对话路径

rules：用于训练RulePolicy

 2.  NLU Training Examples解析及实例分析

NLU的训练数据会根据intent来对用户对话数据进行分类，intent的命名应该与用户需要使用它来完成的任务相关，名称应该小写，不要有空格以及特殊字符。

这是样例：

下面的样例中的intent使用了/符号，表示out_of_scope中包含子intent “non_english”，这种使用方式也被faq或者chitchat使用：

如果使用自定义NLU组件时，也可以使用扩展格式，如下面的样例中使用了metadata，metadata可以包括任意的key-value数据，而自定义组件可以使用metadata数据进行分析：

也可以在intent这一层指定metadata：

关于training数据格式：

Rasa使用YAML来管理所有训练数据，包括NLU数据，stories和rules。你可以使用多个YAML文件来划分训练数据，每个文件可以包括任意NLU数据，stories和rules构成的组合。训练数据解释器会根据最上层的key来判断训练数据类型。Domain也使用YAML格式，可以定义多个domain文件，domain包括了responses和forms的定义。

 3.  NLU Entities解析及实例分析

在训练数据中使用entity的名称来进行标注，除了名称之外，还可以使用synonyms，roles，groups来进行标注。

关于entity提取，你可以使用训练数据来训练一个机器学习模型，也可以定义正则表达式来使用基于字符pattern的RegexEntityExtractor来提取entity信息。在提取entities需要考虑使用目的，如果是不需要处理的用户信息则不需要提取：

这里使用了正则表达式来提取信息：

下面是entity的标注样例：

nlu:

- intent: check_balance

  examples: |

    - how much do I have on my [savings](account) account

    - how much money is in my [checking]{"entity": "account"} account

    - What's the balance on my [credit card account]{"entity":"account","value":"credit"}

标注一个entity的所有可能的语法格式如下：

[<entity-text>]{"entity": "<entity name>", "role": "<role name>", "group": "<group name>", "value": "<entity synonym>"}

 4.  NLU Synonyms解析及实例分析

通过同义词技术把提取的entity信息映射为一个统一的值，具体样例如下：

也可以使用下面这种方式：

nlu:

- intent: check_balance

  examples: |

    - how much do I have on my [credit card account]{"entity": "account", "value": "credit"}

- how much do I owe on my [credit account]{"entity": "account", "value": "credit"}

 5.  NLU Regular Expressions for Intent Classification解析及实例分析

当使用RegexFeaturizer时，并不是把正则表达式作为rule来对意图进行分类，它只提供一个特征给意图分类器用于学习意图分类的方式（patterns）。目前所有的意图分类器都利用可用的正则表达式特征进行意图分类。一个正则表达式的名称是易读的，从而帮助人们记忆它的用途，这个名称不需要匹配任何意图或者实体名称，例如针对一个help请求的正则表达式描述如下：

所匹配的intent可以是greet，help_me，assistance等等。为了尽可能减少匹配的词汇数量，建议使用\bhelp\b而不是help.*。

 6.  NLU Regular Expressions for Entity Extraction解析及实例分析

-使用正则表达式为RegexFeaturizer创建特征，当使用RegexFeaturizer时，一个正则表达式提供一个特征帮助模型学习在意图/实体和匹配了正则表达式的输入之间的联系。在这种情况下，RegexFeaturizer只是提供特征给实体提取器，在训练数据中需要包含足够的正则表达式样例，从而帮助实体提取器学习使用正则表达式特征。目前用于实体提取的正则表达式特征只能被组件CRFEntityExtractor和DIETClassifier所使用。

-在基于rule的实体提取中使用正则表达式，这时需要使用RegexEntityExtractor，当使用这个组件时，正则表达式的名称需要和你想提取的实体的名称匹配，如在下面的例子里，使用正则表达式来提取10-12位的账号信息，RegexEntityExtractor不要求使用训练数据来学习提取实体，但是需要至少有两条标注了账号实体信息的数据来让NLU模型在训练时能够把账号注册为一个实体。

 7.  NLU Lookup Tables解析及实例分析

Lookup tables是用于产生对大小写不敏感的正则表达式模式的词汇列表，它们的用法与正则表达式一样，通常在pipeline中与RegexFeaturizer和RegexEntityExtractor一起组合使用。你可以使用lookup tables帮助提取已经预定义了值的实体。尽可能地保持lookup tables的内容是具体的，如在提取国家名称的样例中，你可以把所有国家名称添加到一个lookup table里：

当lookup tables和RegexFeaturizer结合一起使用时，提供足够的你想要匹配的intent或者entity的训练数据以便模型能够学习使用正则表达式作为一个特征。当lookup tables和RegexEntityExtractor结合一起使用时，提供至少两条标注这个entity的数据以便让NLU模型在训练时能够注册这个entity。

 8.  NLU Entities Roles and Groups解析及实例分析

为了在训练数据中区别同一个entity在不同场景中的使用，可以使用role来标记一个entity，譬如在下面的例子中，为了区分在不同地方使用的city，可以指定role为departure或者destination：

- I want to fly from [Berlin]{"entity": "city", "role": "departure"} to [San Francisco]{"entity": "city", "role": "destination"}.

你也可以指定group来对不同的entities进行分组：

Give me a [small]{"entity": "size", "group": "1"} pizza with [mushrooms]{"entity": "topping", "group": "1"} and

a [large]{"entity": "size", "group": "2"} [pepperoni]{"entity": "topping", "group": "2"}

为了正确训练模型，需要为每一种entity和role，group的组合包含足够的训练数据。为了能够使模型进行泛化，需要在训练数据中存在以下变量，如包括fly TO y FROM x。

为了使用指定的role或者group填充slot，在from_entity类型的slot mapping中也需要指定role和group。

 9.  NLU Entity Roles and Groups influencing dialogue predictions解析及实例分析

如果需要通过role和group来影响对话预测，那么需要修改stories来包括所期望的role或者group标签。另外需要在domain文件中为一个entity指定roles和groups。下面的例子演示了如何根据不同地点的用户输出不同的信息：

 10. NLU BILOU Entity Tagging解析

DIETClassifier和CRFEntityExtractor可以指定BILOU_flag，这个flag用于标识在用户输入信息转换成token的列表中，所提取的entity所处的相对位置，譬如在开始位置，在entities中间，或者在结尾等等。使用这个flag可以改进机器学习模型在预测entities时的表现。譬如针对下面的训练数据：

[Alex]{"entity": "person"} is going with [Marty A. Rick]{"entity": "person"} to [Los Angeles]{"entity": "location"}.

在token列表中针对BILOU_flag为true或者false所得到的结果：


————————————————
版权声明：本文为CSDN博主「m0_49380401」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/m0_49380401/article/details/123342915
