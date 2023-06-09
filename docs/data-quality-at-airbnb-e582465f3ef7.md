# Airbnb 的数据质量

> 原文：<https://medium.com/airbnb-engineering/data-quality-at-airbnb-e582465f3ef7?source=collection_archive---------0----------------------->

## 第 1 部分— **大规模重建**

作者:乔纳森·帕克斯，沃恩·考斯，保罗·埃尔伍德

![](img/d2519db9b8cdda57427872d47871cf16.png)

# 介绍

在 Airbnb，我们一直有一种数据驱动的文化。我们组建了一流的数据科学和工程团队，构建了行业领先的数据基础设施，并启动了众多成功的开源项目，包括 [Apache Airflow](https://airflow.apache.org/) 和 [Apache Superset](https://superset.apache.org/) 。与此同时，Airbnb 已经从一个以光速前进的初创公司转变为一个拥有数千名员工的成熟组织。在这一转型过程中，Airbnb 经历了大多数公司都会遇到的典型增长挑战，包括那些影响数据仓库的挑战。这篇文章探讨了 Airbnb 在高速增长期间面临的数据挑战，以及我们为克服这些挑战采取的措施。

# 背景

随着 Airbnb 从一家小型初创公司成长为今天的公司，许多事情都发生了变化。该公司已经涉足新的业务领域，收购了许多公司，并显著改进了产品战略。同时，对我们数据的要求也发生了变化。例如，领导层对数据的及时性和质量有很高的期望，并更加关注成本和合规性。为了确保我们继续满足这些期望，很明显，我们需要对我们的数据进行大规模投资。这些投资集中于解决与所有权、数据架构和治理相关的领域。

***所有权***

在本文描述的数据质量倡议之前，数据资产所有权主要分布在产品团队中，软件工程师或数据科学家是管道和数据集的主要所有者。但是，数据所有权责任没有明确定义，这是出现问题时的一个瓶颈。

***数据架构***

该公司早期建造的大多数管道都是有机构建的，没有明确定义的质量标准和数据架构的总体战略。这导致了臃肿的数据模型，并给一小部分工程师带来了巨大的操作负担。

***治理***

除了需要为数据架构制定一个总体战略之外，Airbnb 还需要一个集中的治理流程，以使团队能够遵守该战略和标准。

# 数据质量倡议

2019 年初，该公司对数据质量做出了前所未有的承诺，并制定了一项全面的计划，以解决我们在数据方面面临的组织和技术挑战。Airbnb 领导层签署了数据质量计划，这是一个大规模的项目，旨在使用新的流程和技术从头开始重建数据仓库。

在制定提高数据质量的综合策略时，我们首先提出了 5 个主要目标:

1.  确保所有重要数据集的明确所有权
2.  确保重要数据始终符合 SLA
3.  确保使用最佳实践按照高质量标准建造管道
4.  确保重要数据是可信的，并经过例行验证
5.  确保数据记录完整且易于发现

以下部分详细介绍了推进这项工作的具体方法，特别关注我们的数据工程组织、架构和最佳实践，以及我们用于管理数据仓库的流程。

# 组织

一旦数据质量计划的势头达到临界点，领导层就重新调整公司有限的数据工程资源来启动项目。这足以解除 Airbnb 最关键数据的障碍；然而，很明显，我们需要团结起来，在 Airbnb 大幅度发展数据工程社区。以下是我们为加快进度所做的更改。

## **数据工程角色**

几年来，Airbnb 都没有正式的数据工程师职位。大多数数据工程工作是由数据科学家和软件工程师完成的，他们以各种不同的名字被招募。这种不一致使得招聘数据工程技能非常具有挑战性，并在职业发展方面造成了一些混乱。为了解决这些问题，我们重新引入了“数据工程师”这个角色，作为工程组织中的一个专业。新的角色要求数据工程师在几个领域都很强，包括数据建模、管道开发和软件工程。

## **组织结构**

我们还致力于一个分散的组织结构，由向产品团队报告的数据工程舱组成(相对于单一的集中数据工程组织)。这种模式确保数据工程师符合消费者的需求和产品的方向，同时确保工程师的临界质量(3 或更多)。团队规模对于提供指导/领导机会、管理数据操作和填补人员缺口非常重要。

为了补充分散的数据工程师团队，我们成立了一个中央数据工程团队，开发数据工程标准、工具和最佳实践。该团队还管理与任何产品团队都不协调的全球数据集。

## **社区**

我们创建了新的沟通渠道来更好地连接数据工程社区，并建立了一个跨组织决策的框架。我们成立了以下小组来解决这些差距:

*   **数据工程论坛** —每月一次的数据工程师全体会议，旨在级联上下文并从更广泛的社区收集反馈。
*   **数据架构师工作组**——由来自全公司的高级数据工程师组成。负责制定主要的架构决策，并对 Midas 认证进行审查(见下文)。
*   **数据工程工具工作组**——由全公司的数据工程师组成。负责开发数据工程工具和工作流程的愿景。
*   **数据工程领导小组** —由数据工程经理和我们最资深的个人贡献者组成。负责组织和招聘决策。

## **招聘**

我们改进了数据工程师的招聘流程，并为发展我们的数据工程实践分配了大量员工。我们特别重视让高层领导参与进来，为我们做出将在未来几年影响本组织的决策提供指导。这是一项持续的努力。

# 架构和最佳实践

下一步是统一一套通用的架构原则和最佳实践来指导我们的工作。我们为管道实现的数据建模、操作和技术标准提供了全面的指导方针，这将在下面讨论。

## 数据模型

该公司最初的分析基础“core_data”是一个为易用性而优化的星型模式数据模型。它是由一个中心团队构建和拥有的，并且整合了许多资源——通常跨越不同的主题领域。这种模式在 2014 年效果极佳；然而，随着公司的发展，管理变得越来越困难。基于这一认识，很明显，我们未来的数据模型应该经过深思熟虑的设计，并避免集中所有权的缺陷。

与此同时，该公司构建了 [Minerva](/airbnb-engineering/how-airbnb-achieved-metric-consistency-at-scale-f23cc53dea70) ，这是一个被广泛采用的平台，它对指标和维度进行编目，并计算这些实体之间的连接(以及其他功能)。鉴于其广泛的功能和广泛的采用，显然 Minerva 应该继续在我们的数据架构中发挥核心作用，我们的数据模型应该发挥 Minerva 的优势。

基于这一背景，我们设计了新的数据模型，以遵循 2 个关键原则:

1.  表必须被规范化(在合理的范围内),并且依赖尽可能少的依赖项。Minerva 承担了跨数据模型连接的重任。
2.  描述相似领域的表格被分组到主题区域中。每个主题领域必须有一个单独的所有者，这自然与单独一个团队的范围相一致。所有权应该是明显的。

规范化数据和基于主题领域的数据模型在数据建模领域中并不是新的想法，它们最近有了很大的复兴(参见其他组织最近关于“数据网格”架构的博客文章)。我们发现这一理念特别有吸引力，因为它解决了我们以前的挑战，并与我们的数据组织结构保持一致。

## 数据技术

我们还对管道实施的建议进行了彻底的修改。这将在下面讨论。

***Spark 和 Scala***

当我们开始数据质量计划时，Airbnb 的大多数关键数据都是通过 SQL 编写的，并通过 Hive 执行。这种方法在工程师中不受欢迎，因为 SQL 缺乏函数式编程语言的好处(例如代码重用、模块化、类型安全等)。与此同时，Spark 已经成熟，该公司在这一领域的专业知识也在不断增长。出于这些原因，我们转向了 Spark，并把 Scala API 作为我们的主要接口。同时，我们投资了一个通用的 Spark 包装器来简化读/写模式和集成测试。

***测试***

我们需要改进的另一个领域是我们的数据管道测试。这降低了迭代速度，使得外人很难安全地修改代码。我们要求在构建管道时进行全面的集成测试，作为我们持续集成过程的一部分。

***数据质量检查***

我们还构建了新的工具来执行数据质量检查和异常检测，并要求在新的管道中使用它们。异常检测在防止新管道出现质量问题方面尤其成功。

## 操作

数据操作是另一个改进的机会，所以我们确保在这方面设置严格的要求。所有重要的数据集都需要有一个着陆时间的 SLA，并且管道需要配置寻呼功能。

# 管理

## 过程

当我们着手重建我们的数据仓库时，很明显，我们需要一种机制来确保数据模型之间的内聚性，并跨团队维护高质量的标准。我们还需要一种更好的方式向最终用户展示我们最值得信赖的数据集。为此，我们启动了 Midas 认证流程(如下图所示)。

![](img/375a1867c3fffdea0e1bd53e2eff4539.png)

Diagram of the Midas certification process, described in detail below.

Midas 流程要求利益相关方在建造管道之前首先统一设计规范。这是通过一个 Spec 文档来完成的，该文档为度量和维度、表模式、管道图提供了外行人的描述，并描述了非显而易见的业务逻辑和其他假设。一旦规范获得批准，数据工程师就可以根据商定的规范构建数据集和管道。由此产生的数据和代码，然后审查，并最终授予认证。认证标志在所有面向消费者的数据工具中都是可见的，并且认证数据在数据发现工具中是优先的。

我们将在以后的帖子中提供更多关于 Midas 认证过程的细节。

## 有责任

最后但同样重要的是，我们创建了新的机制来确保与数据质量相关的问责制。我们更新了报告数据质量缺陷的流程，并创建了每周缺陷审查会议，以讨论高优先级缺陷并协调纠正措施。我们还要求团队将数据管道 SLA 纳入其季度 OKR 规划。

# 结论

随着公司的成熟，对其数据仓库的需求会发生显著的变化。为了满足 Airbnb 不断变化的需求，我们成功地重建了数据仓库，振兴了数据工程社区。这是公司范围内数据质量计划的一部分。

数据质量倡议通过全方位的方法解决各个层面的问题，实现了这一振兴。这包括恢复数据工程功能，为这个角色设置一个高技术标准，以及为这个工程专业建立一个社区。还成立了一个新的团队来开发数据工程专用工具。该公司还开发了高度自以为是的架构和技术标准，并启动了 Midas 认证流程，以确保所有新数据都是按照该标准构建的。最后，该公司通过对数据管道所有者设定较高的期望，特别是在操作和错误解决方面，提升了责任。

在这个时间点上，数据质量计划正在全速前进，但是仍然有大量的工作要做。我们正在加快对我们的数据基础的投资，设计我们的下一代数据工程工具和工作流，并制定一项战略，将我们的数据仓库从每日批处理范式转变为接近实时。我们正在积极招聘数据工程领导者，他们将开发这些架构并推动其完成。如果你想帮助我们实现这些目标，请查看 Airbnb 招聘页面。