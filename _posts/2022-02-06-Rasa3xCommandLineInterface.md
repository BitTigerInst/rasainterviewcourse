硅谷硬核Rasa课程、Rasa培训、Rasa面试系列之：Rasa 3.x Command Line Interface
Command Line Interface
CLI (command line interface)提供了简单的常见任务命令。本页面描述了这些命令的行为以及可以传递给它们的参数。
 
rasa init 创建一个新项目，包含训练数据、动作和配置文件。
rasa train使用你的NLU数据和故事来训练一个模型，并将训练过的模型保存在/models中。
rasa interactive 开始一个交互式的学习会话，通过与你的助手聊天来创建新的训练数据。
rasa shell 加载你训练过的模型，让你在命令行上与你的助手交谈。
rasa run 用你训练过的模型启动服务器。
rasa run actions 使用- rasa SDK启动一个action服务器。
rasa visualize 将你的故事可视化。
rasa test 对任何以test_开头的文件测试一个训练过的- rasa模型。对你的NLU训练数据进行80/20的分割。
rasa data convert 在不同格式之间转换训练数据。
rasa data migrate 将2.0域迁移到3.0格式。
rasa data validate 检查域、NLU和会话数据是否不一致。
rasa export 将会话从跟踪器库导出到事件代理。
rasa evaluate markers 从现有的跟踪器存储中提取标记。
rasa X 以本地模式启动- rasa X。
rasa -h 显示所有可用的命令。
rasa init
这个命令为你设置了一个完整的助手，带有一些示例训练数据:

它创建以下文件:


它会询问您是否想要使用这些数据训练一个初始模型。如果您回答否，则models目录将为空。任何默认的CLI命令都需要这个项目设置，所以这是开始的最佳方式。您可以运行rasa train, rasa shell和 rasa test，而无需任何额外的配置。

rasa train
下面的命令训练了一个Rasa开源模型:


如果您的目录中有已存在的模型(默认情况下在models/下)，那么只有模型中已更改的部分将被重新训练。例如，如果你只编辑NLU的训练数据而不编辑其他内容，那么只有NLU部分会被训练。

如果你想单独训练NLU或对话模型，你可以运行rasa train NLU或rasa train core。如果你只为其中一个命令提供训练数据，默认情况下rasa train将回到这些命令中的一个。

Rasa train将把训练过的模型存储在——out, models/默认情况下定义的目录中。默认情况下，模型的名称是.tar.gz。如果您想要以不同的方式命名您的模型，您可以使用——fixed model-name标志指定名称。

以下参数可用于配置训练过程:



Incremental training 增量训练
新2.2版本：这个特性是实验性的。我们引入了一些实验性的功能，以从我们的社区获得反馈，所以我们鼓励您尝试一下!但是，将来可能会更改或删除这些功能。如果你有反馈(积极的或消极的)，请在Rasa论坛上与我们分享。

为了提高助手的性能，练习对话驱动开发(（Conversation-Driven Development，CDD）并根据用户与助手的交谈方式添加新的训练示例是很有帮助的。您可以使用rasa train —finetune来初始化一个已经训练过的模型的管道，并在包含其他训练示例的新训练数据集上进一步微调它。这将有助于减少新模型的训练时间。

默认情况下，该命令从models/目录中获取最新的模型，如果你有一个想要改进的特定模型，你可以通过运行rasa train ——finetune 来指定它的路径。与从头开始训练相比，对模型进行微调通常需要更少的时间来训练机器学习组件，如DIETClassifier、ResponseSelector和TEDPolicy。要么使用定义比以前更少的epoch的模型配置进行微调，要么使用标志——epoch-fraction。——epoch-fraction将在模型配置文件中为每个机器学习组件指定epoch的一部分。例如，如果DIETClassifier配置为使用100个epoch，指定——epoch-fraction 0.5将只使用50个epoch进行微调。

你也可以分别使用rasa train nlu -finetune和rasa train core -finetune来微调nlu -only或对话管理模型。

为了能够对模型进行微调，必须满足以下条件:
1、所提供的配置应该与用于训练正在进行微调的模型的配置完全相同。唯一可以改变的参数是单个机器学习组件和策略的epoch。

2、用于训练基本模型的标签集(意图、动作、实体和槽)应该与用于微调的训练数据中的标签集完全相同。这意味着您不能在增量训练期间向您的训练数据添加新的意图、动作、实体或插槽标签。您仍然可以为每个现有的标签添加新的训练示例。如果您已经在训练数据中添加/删除了标签，则需要从头开始训练管道。

3、要微调的模型使用当前安装的rasa版本的MINIMUM_COMPATIBLE_VERSION进行训练。

rasa interactive
你可以在本地模式下使用Rasa X在UI中进行交互学习，查看文档了解更多细节。
如果你更喜欢使用命令行，你可以通过运行以下命令来启动一个交互式学习会话:


这将首先训练一个模型，然后开始一个交互式shell会话。然后，您可以在与它交谈时纠正助手的预测。如果UnexpecTEDIntentPolicy包含在管道中，action_unlikely_intent可以在任何会话回合被触发。随后，将显示如下信息:

正如该消息所言，这表明您已经探索了一个会话路径，根据当前的训练故事集，这是意料之外的，因此建议将此路径添加到训练故事中。与其他bot操作一样，您可以选择确认或拒绝运行此操作。如果你使用 --model参数提供一个训练过的模型，训练将被跳过，而该模型将被加载。

在互动学习过程中，Rasa将从训练数据中绘制出当前的对话和一些类似的对话，以帮助您跟踪自己的位置。会话一开始，您就可以在http://localhost:5005/visualization.html上查看可视化。生成此图可能需要一些时间。要跳过可视化，运行rasa interactive --skip-visualization。
以下参数可用于配置交互式学习会话:




交互式示例：
rasa interactive --config E:\starspace\my_rasa\config.yml --domain E:\starspace\my_rasa\domain.yml --model E:\starspace\my_rasa\models\20211227-124216-active-fort.tar.gz









Rasa官网链接： https://rasa.com/docs/rasa/
Gavin大咖课程信息分享：
NLP 高手之路101课（模型、算法、论文、源码、案例 + 1年答疑）
Rasa 3.x 源码高手之路：系统架构、内核算法、源码实现详解
Gavin大咖简介
星空智能对话机器人创始人、AI通用双线思考法作者，现工作于硅谷顶级的AI实验室。专精于Conversational AI. 在美国曾先后工作于硅谷最顶级的机器学习和人工智能实验室 
Gavin大咖微信：NLP_Matrix_Space  
联系电话：+1 650-603-1290
联系邮箱：hiheartfirst@gmail.com


Rasa课程链接：https://m.lizhiweike.com/liveroom2/27182089
星空智能对话机器人，是全球设计Rasa课程最全面完整、最体系化、涵盖应用行业和课程内容最多的机构，适应人群：

Gavin大咖课程信息分享
Gavin大咖主编出版Spark系列图书约20本，清华大学出版社最新出版2本新书《Spark大数据商业实战三部曲：内核解密|商业案例|性能调优》第二版、《企业级AI技术内幕：深度学习框架开发+机器学习案例实战+Alluxio解密》，累计原创博客1475篇，涵盖大数据、人工智能、智能对话机器人等内容，博客阅读量达217万次。

Gavin大咖课程信息分享
课程名称：Rasa 3.x 源码高手之路：系统架构、内核算法、源码实现详解

课程介绍：
Rasa是Conversational AI在智能业务对话领域工程落地全球最为成功对话机器人系统，是基于Transformer架构的全球使用最广泛的智能业务对话机器人框架，是NLP技术的集大成者。在当今全球范围各项对比指标综合成绩中，Rasa均处于领先地位：
		本课程致力于彻底解密Rasa 3.x系统架构、内核算法、知识图谱及源码实现：
	具体来说，该系统课程是以下五大课程的合集：
业务对话机器人Rasa 3.x Internals内幕详解及Rasa框架定制实战
业务对话机器人Rasa核心算法DIET及TED论文详解及源码实现
Rasa 3.x 语言理解内核Classifiers架构、算法及源码实现
基于Transformer的对话机器人Rasa Policies架构设计与源码全解
Rasa业务对话机器人Microservices微服务架构内幕与源码全解
课程通过这五大阶段内容，按照循序渐进的学习方式，帮助学员彻底精通Rasa新一代内核架构、算法内幕及源码实现。



课程答疑：
课程提供配套的视频、代码及资料，购买后联系Gavin获得代码及辅助资料。
课程提供1年的技术答疑服务，Gavin老师负责所有课程技术问题的答疑及代码服务。

课程试听：

代理模式下的Rasa微服务Form共1288行源码架构设计及源码逐行解析
1，Action类型的FormAction和LoopAction类型的FormAction区别与联系分析
2，Rasa微服务接口interfaces.py共370行源码逐行解析
3，Rasa SDK中的forms.py共918行源文件逐行解析


课程详情：
*************************************************************************************
阶段1：业务对话机器人Rasa 3.x Internals内幕详解及Rasa框架定制实战
*************************************************************************************
以Rasa 3.x提出的全新一代Graph Computational Backend为核心，从Rasa版本迭代中的Milestones出发来完全解密“One Graph to Rule Them All”背后的技术衍化过程及根本原因，然后以GraphComponent为核心解密其架构内幕机制和运行流程，并抽丝剥茧的剖析自定义Rasa Open Source平台的接口实现、组件源码、组件注册及使用的每一个步骤，最后用一个完整的案例来做示例，并透过Rasa的核心TED Policy近2130行源码剖析及DIET近1825行源码剖析，让学习者不仅有定制Rasa框架能力，更有大量源码鉴赏的能力及高级的对话系统架构设计思维。

第1课：Rasa 3.x Internals解密之Retrieval Model剖析
1，什么是One Graph to Rule them All
2，为什么工业级对话机器人都是Stateful Computations？
3，Rasa引入Retrieval Model内幕解密及问题解析

第2课：Rasa 3.x Internals解密之去掉对话系统的Intent内幕剖析
1，从inform intent的角度解析为何要去掉intent
2，从Retrieval Intent的角度说明为何要去掉intent
3，从Multi intents的角度说明为何要去掉intent
4，为何有些intent是无法定义的？

第3课：Rasa 3.x Internals解密之去掉对话系统的End2End Learning内幕剖析
1，How end-to-end learning in Rasa works
2，Contextual NLU解析
3，Fully end-to-end assistants

第4课：Rasa 3.x Internals解密之全新一代可伸缩DAG图架构内幕
1，传统的NLU/Policies架构问题剖析
2，面向业务对话机器人的DAG图架构
3，DAGs with Caches解密
4，Example及Migration注意点

第5课：Rasa 3.x Internals解密之定制Graph NLU及Policies组件内幕
1，基于Rasa定制Graph Component的四大要求分析
2，Graph Components解析
3，Graph Components源代码示范

第6课：Rasa 3.x Internals解密之自定义GraphComponent内幕
1，从Python角度分析GraphComponent接口
2，自定义模型的create和load内幕详解
3，自定义模型的languages及Packages支持

第7课：Rasa 3.x Internals解密之自定义组件Persistence源码解析
1，自定义对话机器人组件代码示例分析
2，Rasa中Resource源码逐行解析
3，Rasa中ModelStorage、ModelMetadata等逐行解析

第8课：Rasa 3.x Internals解密之自定义组件Registering源码解析
1，采用Decorator进行Graph Component注册内幕源码分析
2，不同NLU和Policies组件Registering源码解析
3，手工实现类似于Rasa注册机制的Python Decorator全流程实现

第9课：基于Transformer的Rasa Internals解密之自定义组件及常见组件源码解析
1，自定义Dense Message Featurizer和Sparse Message Featurizer源码解析
2，Rasa的Tokenizer及WhitespaceTokenizer源码解析
3，CountVectorsFeaturizer及SpacyFeaturizer源码解析

第10课：基于Transformer的Rasa Internals解密之框架核心graph.py源码完整解析及测试
1，GraphNode源码逐行解析及Testing分析
2，GraphModelConfiguration、ExecutionContext、GraphNodeHook源码解析
3，GraphComponent源码回顾及其应用源码

第11课：基于Transformer的Rasa Internals解密之框架DIETClassifier及TED
1，作为GraphComponent的DIETClassifier和TED实现了All-in-one的Rasa架构
2，DIETClassifier内部工作机制解析及源码注解分析
3，TED内部工作机制解析及源码注解分析

第12课：Rasa 3.x Internals解密之TED Policy近2130行源码剖析
1，TEDPolicy父类Policy代码解析
2，TEDPolicy完整解析
3，继承自TransformerRasaModel的TED代码解析

第13课：Rasa 3.x Internals解密之DIET近1825行源码剖析
1，DIETClassifier代码解析
2，EntityExtractorMixin代码解析
3，DIET代码解析



*************************************************************************************
阶段2：业务对话机器人Rasa核心算法DIET及TED论文详解及源码实现
*************************************************************************************
对一个智能业务对话系统而言，语言理解NLU及Policies是其系统内核的两大基石。Rasa团队发布的最重磅级的两篇论文DIET: Lightweight Language Understanding for Dialogue Systems及Dialogue Transformers是其基于在业界落地场景的多年探索而总结出来的解决NLU和Policies最核心的成果结晶： 其中DIET是Intent识别和Entity信息抽取的统一框架，而基于Dialogue Transformers的Transformer Embedding Dialogue (TED)是面向多轮业务对话信息处理和对话Response技术框架。DIET和TED作为Rasa内核已经经过很多版本的迭代优化，即使Rasa 3.x最新一代架构中依然可以看到DIET和TED的核心位置：

可以这么说，掌握这两篇论文是掌握Rasa精髓及背后设计机制的核心之所在。所以星空对话机器人推出了业务对话机器人Rasa核心算法DIET及TED论文内幕详解课程，以抽丝剥茧的方式来逐句解读这两篇论文中蕴含的一切架构思想、内幕机制、实验分析、及最佳实践等所有的密码，以帮助对基于Transformer的对话机器人感兴趣的朋友掌握Rasa内核精髓。
	为了更有效的帮助学员达到从模型算法、架构设计、源码实现等角度融汇贯贯通当今工业级最成功的业务对话机器人平台Rasa，除了在课程中逐行解析Rasa的核心TED Policy近2130行源码及DIET近1825行源码外，课程中还增加了Rasa Internals解密之框架核心graph.py源码完整解析及测试中GraphNode源码逐行解析及Testing分析、GraphModelConfiguration、ExecutionContext、GraphNodeHook源码解析、GraphComponent源码回顾及其应用源码。

课程内容：
第1课：多任务对话Transformer架构的DIET中的Intent和NER算法剖析和对比
第2课：基于Transformer的轻量级多任务DIET语言理解NLU内幕解密
第3课：轻量级多任务Transformer语言理解框架DIET试验分析
第4课：使用Transformer Dialogue具有Context的面向任务的对话系统
第5课：具有上下文和抗干扰能力的Transformer Dialogue对话系统Experiments详解
第6课：基于Transformer的Rasa Internals解密之框架核心graph.py源码完整解析及测试
第7课：基于Transformer的Rasa Internals解密之框架DIETClassifier及TED
第8课：Rasa 3.x Internals解密之TED Policy近2130行源码剖析
第9课：基于Transformer的Rasa 3.x Internals解密之DIET近1825行源码剖析


*************************************************************************************
阶段3：Rasa 3.x 语言理解内核Classifiers架构、算法及源码实现
*************************************************************************************
课程关键字：Rasa、NLU、Intent、Classifier、Graph、Transformer、BERT、Fallback、GraphComponent

课程介绍：
本课程聚焦Rasa 3.x Classifier底层Transformer引擎、DIET论文算法、新一代Graph架构、及源码逐行剖析，具体来说：
1，从Transformer及BERT论文及源码实现入手，解密Rasa Classifiers的底层的ML引擎；
2，以DIET论文算法为基石，彻底剖析Rasa新一代NLU核心技术的算法、架构及源码实现
3，基于Rasa 3.x全新一代的Graph Architecture，彻底剖析Graph视角下Rasa NLU Classifiers所有内幕机制及源码实现
	课程以抽丝剥茧的方式解密Rasa NLU Classifiers的所有的算法内幕、架构机理、运行流程及源码实现，帮助学员彻底掌握Rasa NLU Classifiers这一核心内容。	

课程内容：
第1课： Transformer论文解密、数学推导及完整源码实现
第2课：BERT论文解密、数学推导及完整源码实现
第3课：轻量级多任务NLP系统DIET论文算法解密及架构解析
第4课：轻量级多任务DIET运行内幕及实现细节剖析
第5课：轻量级多任务Transformer语言理解框架DIET试验分析
第6课：Rasa 3.x全新一代可伸缩DAG图架构内幕
第7课：Rasa 3.x Internals解密之定制Graph NLU及Policies组件内幕
第8课：Rasa 3.x Internals解密之自定义GraphComponent内幕
第9课：Rasa 3.x Internals解密之框架核心graph.py源码完整解析及测试
第10课：Rasa 3.x Internals解密之框架DIETClassifier及TED
第11课：Rasa 3.x Internals解密之DIET近1825行源码剖析
第12课：Rasa Fallback Classifier处理对话失败情况三大处理方式内幕及代码实战
第13课：Rasa Fallback and Human Handoff全解
第14课：Rasa FallbackClassifier源码逐行剖析


 
*************************************************************************************
阶段4：基于Transformer的对话机器人Rasa Policies架构设计与源码全解
*************************************************************************************
课程关键字：Transformer、BERT、Dialogue Transformer、Rasa 3.x、Dialogue Policies、GraphComponent、TED Policy、UnexpecTEDIntentPolicy、RulePolicy、MemoizationPolicy、Ensemble

课程介绍：
	通过超过16小时对基于Transformer的Rasa智能业务对话机器人对话Policies的全部源码进行抽丝剥茧的逐行解析：以BERT为出发点，细致的剖析Rasa Policies核心算法Dialogue Transformer论文内幕及源码实现，同时结合Rasa 3.x的Graph Architecture理念，完成的剖析Rasa Policies架构内幕及源码实现。
本课程不仅能够帮助学员彻底掌握Rasa对话策略的内幕机制、架构设计及源码实现，更重要是会具备定制开发对话策略的能力。

课程内容：
	第1课：BERT架构、pretraining预训练、Fine Tuning下游任务微调全生命周期内幕解密
	第2课：BERT预训练Pre-training源码完整实现
	第3课：BERT Fine-tuning数学原理及案例源码解析
	第4课：BERT Paper 论文解密、数学推导及完整源码实现
	第5课：Transformer Dialogue论文原理及算法详解
	第6课：Transformer Dialogue对话系统论文Experiments详解
	第7课：基于Transformer的Rasa Internals解密之框架核心graph.py源码完整解析及测试
	第8课：Rasa 3.x Internals解密之TED Policy近2130行源码剖析
	第9课：UnexpecTEDIntentPolicy源码研读
	第10课：UnexpecTEDIntentPolicy算法源码及IntentTED详解
	第11课：Rasa Memoization对话策略及源码解析
	第12课：Rasa Rule-based Policies架构设计与源码解析
第13课：Rasa RulePolicy完整源码详解
第14课：Rasa对话策略架构设计及Policy接口源码解析
第15课：Rasa Policy完整源码逐行详解
第16课：Rasa对话策略Ensemble算法内幕与完整源码剖析


*************************************************************************************
阶段5：Rasa业务对话机器人Microservices微服务架构内幕与源码全解
*************************************************************************************

课程关键词：Rasa、Microservices、Knowledge Base、Microservices 、Action Server、Rasa Server、Action、Event、ActiveLoop、LoopAction、FormAction、FormValidationAction、CollectingDispatcher、Tracker、DomainDict、TwoStageFallbackAction、Proxy Pattern

课程内容：
	微服务和知识图谱是智能业务对话机器人智能水平高低的决定的因素：是微服务Microservices赋予对话机器人业务处理能力。具备专家领域知识Knowledge的知识图谱系统能够极大的提升业务对话机器人的业务知识和业务对话能力，是智能业务对话机器人提升智能的关键。
	本课程聚焦于Rasa这个全世界工程落地最为成功的智能业务对话机器人框架中的微服务及知识图谱架构设计内幕、运行流程机制、案例代码剖析及Rasa微服务及 知识图谱所有的系统源码分析。具体来说：
	1，彻底解密基于代理模式的Rasa微服务架构机制内幕、运行流程、及消息通信解析
	2，Rasa Server端action.py、loops.py、forms.py、two_stage_fallback.py的源码逐行解析
	3，Rasa SDK端所有Event类型的解析及源码实现、interfaces.py及forms.py源码逐行解析
	4，源码分析和案例相结合剖析Rasa微服务，通过具体的对话机器人案例验证源码分析
	5，课程中还对Rasa Knowledge Base中的ActionQueryKnowledgeBase及实战案例做了透彻剖析	
6，抽丝剥茧的讲解Rasa知识图谱架构原理、流程内幕及其框架的完整源码的逐行分析。
	7，在剖析Rasa知识图谱源码的过程中结合具体的案例，帮助学习者通过案例透彻理解Rasa知识图谱框架的每一行源码内幕。
	学习完本课程，可以彻底掌握Rasa微服务开发并实现任意复杂度的Rasa对话机器人的业务功能，同时能够用Rasa整合实现任意复杂度的Knowledge系统及业务开发功能。

课程大纲：
第1课：Rasa对话机器人业务逻辑Action Servers架构设计与核心运行流程解密
1，Rasa Server与Action Servers交互关系解析
2，请求执行custom action的RESTful中JSON内容详解及示例
3，Action Servers返回的events及responses详解及示例

第2课：Rasa Events剖析及源码详解
1，Event接口分析
2，14大Event剖析及源码详解
3，Loop相关Event分析及源码详解

第3课：Rasa微服务Action自定义及Slot Validation详解
1，Rasa Action剖析及代码示例
2，ValidationAction剖析及代码示例
3，FormValidationAction剖析

第4课：Form全生命周期解析及Default Actions剖析
1，Form全生命周期运行内幕
2，Form的高级用法
3，Default Actions详解

第5课：Rasa微服务四大组件全解
1，Rasa Actions和Tracker详解
2，Rasa Dispatcher及Event详解
3，关于Metadata的使用及Action Server启动参数详解

第6课：Rasa Core action.py源码剖析之常见类、工具方法及微服务通信类
1，三大常见类Action、ActionBotResponse、ActionListent源码逐行剖析
2，action.py中工具方法源码详解
3，微服务请求核心RemoteAction源码逐行剖析及AIOHTTP使用详解

第7课：Rasa系统内置Action源码逐行解析
1，ActionSessionStart、ActionRestart、ActionBack源码逐行解析
2，ActionEndToEndResponse、ActionDefaultFallback、ActionRevertFallbackEvents源码逐行解析
3，ActionDeactivateLoop、ActionUnlikelyIntent、ActionExecutionRejection源码逐行解析
4，ActionDefaultAskAffirmation、ActionDefaultAskRephrase、ActionExtractSlots源码逐行解析
5，extract_slot_value_from_predefined_mapping源码逐行解析

第8课：Rasa ActiveLoop、LoopAction及TwoStageFallbackAction源码逐行剖析
1，ActiveLoop源码逐行剖析
2，Rasa LoopAction源码逐行剖析
3，TwoStageFallbackAction源码逐行剖析

第9课：654行Rasa LoopAction类型的FormAction源码逐行剖析
1，LoopAction类型的FormAction运行机制和业务开发意义分析
2，Slots状态的管理、校验、和维护源码解析
3，do方法和is_done方法深度分析

第10课：代理模式下的Rasa微服务Form共1288行源码架构设计及源码逐行解析
1，Action类型的FormAction和LoopAction类型的FormAction区别与联系分析
2，Rasa微服务接口interfaces.py共370行源码逐行解析
3，Rasa SDK中的forms.py共918行源文件逐行解析

第11课：Rasa与Knowledge Base进行整合示例分享、架构剖析、及程序开发三步骤
1，Rasa与Knowledge Base整合具体案例分析
2，Rasa与Knowledge Base三层架构及运行流程剖析
3，Rasa与Knowledge Base程序开发的三步骤分析

第12课：Rasa Knowledge Base案例代码、工作机制及自定义详解
1，ActionQueryKnowledgeBase分析及案例解析
2，Knowledge Base Actions工作机制解密
3，Knowledge Base Actions自定义详解

第13课：Knowledge Base功能详解及源码实现
1，Knowledge Base导入包分析
2，KnowledgeBase类源码逐行解析
3，InMemoryKnowledgeBase类源码逐行解析

第14课：ActionQueryKnowledgeBase源码逐行解析
1，对objects的操作源码详解
2，对attributes的操作源码详解
3，ActionQueryKnowledgeBase预设值解析

第15课：ActionQueryKnowledgeBase的utils.py源码逐行解析
1，utils.py高频使用的Tracker源码解析
2，默认名称配置解析
3，utils.py文件源码逐行解析

特别说明：

参与学习星空智能对话机器人的Rasa完整体系课程可参加工业与信息化部AI考试，报名费另收500元，学员自愿报名参加，Gavin导师全程做考试辅导，证书编号可在官网查询。考试时间以工业与信息化部安排为准。
 

硅谷硬核Rasa课程 | Rasa培训 | Rasa面试
 全球设计Rasa课程行业应用最全面广泛、课程内容体系最多的机构
Rasa技术咨询：NLP_Matrix_Space



