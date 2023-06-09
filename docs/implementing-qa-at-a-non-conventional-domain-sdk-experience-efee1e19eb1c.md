# 在非常规领域实施 QA(SDK 经验)

> 原文：<https://medium.com/globant/implementing-qa-at-a-non-conventional-domain-sdk-experience-efee1e19eb1c?source=collection_archive---------1----------------------->

![](img/d31753c3431971d1e44ce1b1d7dcce02.png)

# 情况

我们的团队面临的挑战是为游戏行业的软件开发工具包(SDK)的开发实现一个 QA 框架

根据我们的经验，下面你可以找到一些关键因素，如果你在路上遇到类似的情况，这些因素可能会有所帮助

# 接受挑战

首先，拥有在非常规产品上实施质量框架的挑战，并把它作为一个机会，将为团队参与和积极的态度奠定基础

在我们的案例中，挑战与最终用户的类型、可交付物的生态系统以及远离标准的遗留代码(在其他人之间)有关。

因此，我们将此视为改变视角、学习和创新的机会

# 拥有企业

根据我的经验，许多有经验的测试人员(就测试有效性和功能覆盖率而言)是最了解产品的人。

他们作为专家用户深入参与到产品清单中，如果你问他们产品的优点和缺点，他们可以很快做出反应。

记住这一点，早期参与领域和产品开发背后的业务目标对成功至关重要

# 结交新朋友

如果你面对一个新的领域，最好创建你的人际网络，并与所有利益相关者开始对话，以填补任何技术或业务知识缺口。

在我们的项目中，我们与 PO、BA、架构师和开发人员密切合作，包括我们和客户在各个讨论层面的讨论:从产品时间表和里程碑到用户故事子任务，从产品生态系统到代码单元测试覆盖。

# 定义被测系统和参与者

清楚地了解被测系统和**参与者**对于设计一个合适的测试策略是至关重要的，尤其是如果这个领域不是很常见的话

刷新 UML 概念，参与者“指定由用户或任何其他与主体交互的系统所扮演的角色”

根据 ISTQB，被测系统(SUT)“是指被测试是否正确运行的系统”,它是**测试对象**

在我们的 SDK 案例中，参与者是特定领域的开发者，比如 API、DB、.net、软件包(我们必须穿上他们的鞋子),从部署产品的角度来看，参与者是编译产品的消费者

# 设计动态测试策略

与整个团队(不仅仅是 QAs)一起定义测试策略有助于提高覆盖率、效率、质量的共同责任以及团队参与度。

在我们的案例中，我们从头开始开发一个 SDK 供本地使用，另一方面开发一个生态系统来托管产品(与标准的电子商务交易完全不同),我们需要确保两者的质量

我们会见了整个团队，并使用了 Marick 的象限。设计的战略是动态的，因为产品不断变化，不断创新(运行概念验证和试点)，所以我们经常审查和调整方法。

# 创建您的食谱

在非标准软件项目的情况下，使用已知的“配方”或预先设计的 QA 流程的机会很低，因此您必须创建自己的配方，并请 QA 框架设计方面的主题专家(SME)来“品尝”

我们做了大量的研究，准备了我们的框架，将我们的 QCs 在不同实践和工具上的经验混合在一起，并且我们用一个 SME 验证了它

# 关注验证和非功能性需求

如果是一个新的领域，尽早讨论法规、标准、领域细节和非功能性需求将有助于避免临近发布日期时出现意外

在我们的例子中，我们试图在项目开始时了解领域、它的规则和标准

# 进行敏捷测试

加强上面的项目，如通用测试策略、早期参与、与开发人员的交互、BDD 设计和自动化，意味着敏捷测试的实现

我们的 BDD 实现包括一个灵活的自动化框架来快速响应变化

# 强化技能并适应项目

在非常规软件产品所要求的专业化场景下，只有“多面手”的 QC 团队会使学习曲线变得复杂和有风险，所以要确保至少有一个团队成员拥有与**参与者**相似的技能。他将是其他 QC 团队用例的“教练”

在我们的案例中，对于 SDK，我们包括了一个拥有大量专业知识的 QA(在我们的案例开发中),经过几次冲刺后，我们确认这是一个非常好的决定

# 结论

过了一段时间，这是一次很棒的经历，上面提到的所有关键因素帮助我们最小化业务和技术差距，管理不确定性，实施敏捷测试，让整个团队参与质量保证

作为探索新方法的空间，我会提到测试用例生命周期，因为在创新产品的情况下，有许多 POC，在架构上试验和来回，导致测试用例的周期性审查、更新(和废弃)

总之，目前越来越多的非常规项目正在出现，它们不能用我们过去工作的传统策略来开发。

从心态的改变开始，基于灵活性、好奇心，当然，适应新的领域并继续实用主义来实施实践和工具(为什么不开发自己的)，确保质量(并享受旅行)的机会将会很高。

创新、新领域、新参与者、不同的利益相关者即将出现，我们应该接受挑战，探索新世界，提升自己的技能