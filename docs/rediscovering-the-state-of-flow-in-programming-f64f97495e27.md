# 重新发现心流的状态

> 原文：<https://medium.com/capital-one-tech/rediscovering-the-state-of-flow-in-programming-f64f97495e27?source=collection_archive---------7----------------------->

## 关于编程的思考

![](img/dbfe920f2d3fb178556d1ea812d0d701.png)

三个事件的特殊组合一起重新点燃了我对编程流程的兴趣。

第一个是看着[在网飞停下并着火](https://www.netflix.com/title/70302182)。这是一个关于最早的个人电脑编程的节目，许多集展示了小型工程师团队致力于突破性项目，以实现个人计算的到来。第二件事是阅读卡尔·纽波特的书[深度工作](http://calnewport.com/books/deep-work/)，这本书鼓励人们排除杂念，集中注意力，解决最棘手的问题。第三起发生在工作中。我是 Capital One 的工程副总裁，正带领一个团队将我们的部分分析环境迁移到云中。这种基础设施已经存在了十多年，并且经过多年的发展，产生了数以千计的支持业务的表。

所以，我有这三个要素:看着*停止并着火*重新点燃了我再次编程的欲望，阅读*深度工作*激励我深入专注于一项任务，电子表格处理问题是我需要解决的事情。*那么这个欲望、这个灵感和这个问题是如何结合成心流的呢？*

![](img/5f09194611eb364cf772de6f70a2858a.png)

为了回答这个问题，我想多关注一下第三个事件——我们必须迁移的数千个表以电子表格的形式发送给我，让我进行检查和处理。我们在一个紧张的时间框架内工作，我需要在这个限制内迁移表。我心想，*“如果我能减少表的数量，这将启动我们的数据转换工作，并为我们提供一个成功的窗口。”*

当我第一次打开电子表格时，我立刻就有了认知超载。它看起来像电脑屏幕上的随机字节。当我仔细观察时，我开始发现数据中的模式。这实际上不是随机的，记录遵循严格的命名规则，每个单词用下划线隔开。

我认为解决这个问题的最好方法是使用 Python 自动对电子表格进行分类。每当我遇到下划线时，我会把单词分成小块。使用拆分的单词，我可以将单词的子集分类到桶中。我已经有一段时间没有解决这类问题了。第一天，我磕磕绊绊地学习如何安装 Python，观看关于数据结构和控制结构如何在 Python 中工作的教学视频。第二天的时间花在了构建小应用程序来读取文件和循环记录上。第三天上午 10 点左右，我坐下来，喝了杯咖啡，又开始了我的计划。到了这个阶段，我已经重新学习了 Python 的工作方式，并构建了基本的结构，这让我可以将注意力转移到对记录进行分类的业务上。

![](img/ceb79b6cefd1e4f75cee791479165b03.png)

然后就发生了。我在下午 2:30 左右“醒来”,记得我正坐在客厅里编程。我如此专注于任务，以至于在创建分类算法时，我进入了一种永恒的状态。就像处于恍惚状态——我被深深地吸引到工作中，完全失去了时间感。

我已经进入了一种“心流”状态

> 然后就发生了。我在下午 2:30 左右“醒来”,记得我正坐在客厅里编程。我如此专注于任务，以至于在创建分类算法时，我进入了一种永恒的状态。就像处于恍惚状态——我被深深地吸引到工作中，完全失去了时间感。*我进入了一种“心流”状态*

心流只存在于当下，只有当它消失时，你才知道你曾在其中。心流是挑战、你的技能和强烈关注的结合。心流不是编程的专属，有很多机会可以进入心流的状态。运动、烹饪、绘画和演奏音乐都是心流的好例子。实现流的结果是将我的组织中必须迁移的表的数量减少了 75%。

![](img/f743f3bda3cb4db6dc961278ed8a72fd.png)

当我反思我的经历时，我非常感激我能重新发现心流，并记住工程师们有特权体验这是他们工作的一部分。作为 Capital One 的工程领导者，我认为我有责任为工程师提供体验心流的机会，给他们挑战极限的挑战性任务，同时提供他们成功所需的空间。

这就是我热爱构建软件的原因。我希望这也是你喜欢它的原因之一。

*声明:这些观点仅代表作者个人观点。除非本帖中另有说明，否则 Capital One 不属于所提及的任何公司，也不被其认可。使用或展示的所有商标和其他知识产权都是其各自所有者的所有权。本文为 2018 首都一。*