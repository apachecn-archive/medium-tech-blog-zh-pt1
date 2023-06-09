# 项目设置流程编排器的性能增强

> 原文：<https://medium.com/walmartglobaltech/performance-enhancements-to-the-item-setup-orchestrator-6a733b99591d?source=collection_archive---------9----------------------->

![](img/4038f7f3543c5e78f012a7295ac78088.png)

最近，项目设置团队意识到其系统未能以最佳方式运行。去年，沃尔玛的销售额和商品增长了 40%，但机器数量和产能却没有同样的增长。因此，项目设置团队必须优化其系统并提高性能。

性能改进将分两部分进行解释。首先，简要概述沃尔玛的商品设置后端，其次，实际的性能变化。

沃尔玛的后端是构建前端的数据被创建的地方。例如，沃尔玛有一个包含你在网上看到的图片的数据库。当你点击沃尔玛网站上的页面购买高露洁牙膏时，Item Read Orchestrator 会从沃尔玛的产品数据库中提取图片，并从沃尔玛的价格数据库中提取价格。

本文将重点介绍这些前端数据库如何获取数据，或者换句话说，商家和卖家如何使用一个名为 Hyperloop 的系统将数据输入沃尔玛的数据库。

![](img/908b9b86194816f4b999bda11b8045f4.png)

Hyperloop 由三个系统组成:规范解析器、Feed Ingestor 和项目设置 Orchestrator。在线销售商品的第一步是存储商品的所有数据。例如，商品由卖家信息、报价信息、产品描述信息、定价信息、物流信息和运输信息组成。卖家信息由一系列属性组成，例如，卖家名称、卖家描述、卖家 id 以及类似其标识符的项目特定信息等。有时存储数据更复杂，因为某些数据片段相互依赖。例如，如果不更改卖家 id，就不能更改卖家名称。

下一个系统是数据吸收器。数据吸收器是一个状态和转换管理器。首先，它跟踪项目属性的更改过程。这一点尤其重要，因为系统的数量和跨系统的信息分组。例如，物流信息依赖于定价和卖家信息。如果卖家信息发生变化，物流和其他下游系统也需要循环和更新。此外，如果一个系统出现故障，数据吸收器需要重试并重新处理整个系统。

数据吸收器执行的另一个重要功能是数据转换。来自商家的数据从 excel 文档转换成沃尔玛数据对象。

最后一个系统是项目设置协调器。item system orchestrator 将数据从数据入口传输到负责数据的每个系统。项目设置协调器将价格数据从数据导入器导入价格系统本身。为了方便这些最终事务，项目设置 orchestrator 充满了 REST APIs 和 kafka 隧道。当您希望将消息链接起来并同时向多个资源层发送信息时，orchestrator 特别有用。例如，成本信息被发送到三个系统:供应商系统、物流系统和成本系统。

![](img/c354f104ffaea09d6f2a2c71a2307a96.png)

现在，您已经了解了这个生态系统，我们可以开始讨论假期期间所做的性能改进了。item setup orchestrator 经过了优化，因为它耗时最长，涉及的系统最多，并且有最大的优化空间。

**性能增强**

ISO 在三个方面进行了优化。首先，我们为特定的工作拆分和专用机器。ISO 执行的某些工作具有较高的优先级和较短的 SLA(服务水平协议)。服务水平协议是系统必须完成处理任务的时间长度。因此，使用这些不同的优先级，我们可以专用机器和资源。第二个优化是添加内部和外部分布式缓存，以最大限度地减少外部读取。第三个优化是使用 yourkit 小心管理内存。

ISO 最重要的功能是设置商品和商品的价格。这些工作非常关键，需要很短的 SLA。一旦商家更新价格，价格就应该在网上反映出来，同样，ISO 有两个小时的时间用收到的商家数据更新每个系统。ISO 专用 60 台机器来处理它收到的所有项目更新。此外，一旦资源可用于执行另一个项目设置，机器将开始处理下一个项目设置。这些项目设置更新的优先级将高于这些机器上的其他作业。ISO 每天执行大约 2000 万个项目设置，峰值容量大约为 4000 万个项目设置。

价格更新的优先级略有不同，因为它们是通过 rest 更新直接更新的。项目设置通过一个卡夫卡主题发生。卡夫卡的一个很好的形象化就是流水线。卡夫卡专题是同一阶段的一长串产品。消费者 ISO 选择从装配线上取走产品的速度。

rest 调用是相反的，因为不是 ISO 选择它前进的速度，而是 ISO 的调用者决定项目更新的速度。ISO 使用负载平衡器以两种方式处理 rest 调用。首先，一个负载平衡器将来自用户的其余调用分配给 60 台机器。负载平衡器以循环方式发送消息，这意味着每台机器获得相同的流量；第一条消息发送到第一台机器，第二条消息发送到第二台机器，然后消息循环。第 61 条消息返回到第一台机器。在消息通过负载平衡器、ISO 速率限制后，如果流量和消息速度对于下游服务来说太快，ISO 将节流并返回到服务不可用的 rest 调用。价格管理器将重试该服务可用的消息。ISO 每天接收大约 6000 万条价格更新，峰值约为 8000 万条消息。

为了理解第二个优化，引入本地和外部分布式缓存。缓存在编排中尤其重要，因为 ISO 中并非所有操作都是写操作。大量的操作是读取。写操作是系统中的数据被改变的地方。读取操作是指系统中的数据不发生变化。在读取操作中，您可以从本地或外部缓存中读取，而不是调用外部系统，这既费时又可能失败。缓存是内存优化，其中存储了以前的数据副本。例如，在沃尔玛，ISO 从定价资源层查找一瓶水的价格，并将价格存储在本地缓存中。下次 ISO 查找相同水瓶的价格时，ISO 可以从本地缓存中检索价格，而不必再次从定价资源层查找数据。从本地缓存读取比从定价资源层读取快 20 多倍。本地缓存的缺点是容量有限，并且在搜索之前必须查找数据。本地缓存只能存储大约 20 分钟的数据。有限的容量部分由外部分布式缓存解决。当写入本地缓存时，所有机器还异步写入一个更大的外部数据库。外部数据库由系统中的所有机器共享。在缓存中查找数据时，您不仅可以获得单台机器的历史记录，还可以获得所有机器的历史数据。在这两者之间，我们能够将读取调用的数量减少 25%，将整个项目设置过程的速度提高了 5%以上。我们所有缓存的总命中率接近 15%。

![](img/752709d72a33a26e2cb72348bb6e2b8a.png)

让我们继续第三个优化分析，寻找瓶颈。Yourkit 是一个 java 分析器，用于查找内存泄漏、瓶颈和其他性能优化。它特别有用，因为您可以检查应用程序中的每个线程，并查看哪些 java 类花费的时间最长，使用的内存最多。在分析我们的应用程序时，我们发现了两个瓶颈:日志和 JSON 序列化。因为如此多的消息流过我们的系统，我们不断地将消息从字符串转换成 java 对象，并将这些消息写入日志，以便调试和跟踪发生的事情。我们努力优化这两个部分。第一版 log4j 有很多已知的死锁，下一代 log4j，log4j2 比老版本快 10 倍。ISO 现在升级到 log4j 的下一个版本。

![](img/485e66d5f4e4a7daa046141079afe132.png)![](img/166a9bcf7b5a0a1cbfd00392756cd640.png)

第二个瓶颈是改变 json 序列化器。项目设置团队选择了 JSON 序列化器 JsonIter。JsonIter 比以前的 JSON 序列化器要快得多，并且特别针对多线程进行了优化，这符合我们应用程序的需求。 [Json 迭代器基准](https://jsoniter.com/benchmark.html)

这三项优化的总和将系统性能提高了 50%。这一性能提升对于冬季假期和 COVID 带来的需求增长至关重要。沃尔玛能够省钱，生活得更好，而且不必购买更多的机器。在过去的假期中，该系统每天完美地处理超过 3 亿笔交易。