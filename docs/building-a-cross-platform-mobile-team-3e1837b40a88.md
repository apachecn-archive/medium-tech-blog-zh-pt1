# 建立跨平台的移动团队

> 原文：<https://medium.com/airbnb-engineering/building-a-cross-platform-mobile-team-3e1837b40a88?source=collection_archive---------4----------------------->

## 让手机适应 React Native 的世界

![](img/12a0472760d1ae314832e47d79995038.png)

*这是* [*系列博文*](/airbnb-engineering/react-native-at-airbnb-f95aa460be1c) *中的第三篇，在这篇博文中，我们概述了 React Native 的使用体验以及 Airbnb 的下一步移动应用。*

除了 React Native 在技术上的无数优点和缺点，我们还了解了 React Native 对一个工程组织意味着什么。采用它比在现有平台上添加新的库或模式要复杂得多。这样做暴露了一些组织挑战。与通常可以解决或有效规避的技术挑战不同，组织挑战可能更难检测、纠正和恢复。值得庆幸的是，我们的移动文化是健康的，但在考虑 React Native 时，有一些事情需要注意。

## React Native 正在两极分化

根据我们的经验，工程师们对 React Native 有各种不同的意见，从称赞它是团结 Android、iOS 和 web 的银弹，到完全反对在他们的团队中使用 React Native。用了之后还是这样。一些团队有着不可思议的经历，而另一些团队则后悔了，回归到了本土。

## 根本原因归因

在 React Native 上工作时，不可避免地会出现错误、改进和性能问题。然而，有许多动人的片段:

1.  反应原生自身移动迅速。
2.  我们同时进行基础设施和功能开发。
3.  工程师们一起学习 React Native，这对每个人来说都是相对陌生的。
4.  我们在开发和生产中调试的文档和指南有时不一致，可能会令人困惑。

因此，通常很难找到问题的根本原因。有时，不清楚问题应该归属于哪个团队，也不清楚问题是否是固有的。

## 反应原生还是原生

一个常见的误解是 React Native 允许您完全不用编写本机代码。然而，这并不是世界的现状。React Native 的本土基础仍不时抬头。例如，文本在每个平台上的呈现方式略有不同，键盘的处理方式也有所不同，在 Android 上，默认情况下活动是在旋转中重新创建的。高质量的 React 原生体验需要两个世界的仔细平衡。这一点，再加上在所有三个平台上平衡专业知识的困难，使得运送一致的高质量体验变得困难。

## 跨平台调试

大多数工程师精通一两个平台。在 Android、iOS 和 React 方面非常优秀的人非常少见。尽管成熟的 React Native 环境中的绝大多数工作都是用 JavaScript 和 React 完成的，但有时构建或调试某些东西需要深入研究 Native。这些情况可能会导致工程师在他们从未使用过的平台上尝试调试问题时陷入专业知识的困境。当工程师由于根本原因归属的困难甚至不确定去哪里寻找时，情况会变得更糟。

## 雇用

尽管我们投资了 React Native，但我们的移动目标和团队仍在同步发展。然而，通过社区中的口口相传，许多人开始将 Airbnb 与 React Native 联系起来，有些人甚至认为我们的应用程序是 100% React Native。尽管这与事实相差甚远，但许多 Android 和 iOS 工程师因此对申请 Airbnb 犹豫不决。以防你是其中之一，[我们仍在招聘](https://www.airbnb.com/careers/departments/engineering)！

## 混合应用很难

一个 100%原生或 100%反应原生的世界的道路是相对简单的。然而，一旦您的代码库中有了混合代码，许多新问题就会出现。你是如何划分你的团队的？团队如何协作？你如何在你的应用中共享状态？你如何确保事情得到测试？工程师如何跨三个平台有效调试？你如何决定一个新特性使用什么平台？你如何在你的组织中雇佣和分配资源？如果你沿着这条路走下去，这些都是不可避免会出现的非平凡解决方案的问题。

## 三种开发环境

为了成为一名高效的 React 原生工程师，拥有一个稳定且最新的 React 原生、Android 和 iOS 环境非常重要。对于像 Airbnb 这样大的组织来说，每个平台都需要大量的时间来设置、学习和更新。仅仅几个星期后再回来通常意味着要花几个小时来更新所有的东西。

## 平衡本地与反应本地

在许多情况下，问题的最佳解决方案跨越本地和反应本地。例如，我们的导航实现大量使用了 Activities 和 ViewControllers，并且它的大部分代码是每个平台固有的。很多时候，不清楚代码是应该用本地语言编写还是用本地语言编写。当然，工程师通常会选择他们更喜欢的平台，这可能会导致不理想的代码。

## 跨平台测试

我们发现，出于方便或舒适的考虑，工程师们主要会在一个平台上开发，而不是在另一个平台上。通常，他们会假设，如果它在他们测试的平台上可以工作，那么在另一个平台上也可以完美地工作。大多数情况下，这是真实的，这证明了 React Native 的强大。然而，它经常是不真实的，以至于在 QA 周期的后期或生产中导致频繁的问题。

## 分割团队

使用 native 和 React Native 工作的团队经常面临技术和沟通两方面的挑战。一旦代码库在 native 和 React Native 之间分裂，代码就会变得支离破碎。共享业务逻辑、模型、状态等。变得更具挑战性，工程师不再具备处理整个流程的专业知识。我们从一开始就知道会发生这种情况，但我们认为这可能会通过增加与 web 的合作来平衡。一些团队确实开始在网络和移动设备上共享资源和代码，但是大多数都没有意识到这种潜在的好处。

## 感知迭代速度

我们对 React Native 的定性目标之一是提高开发速度。通常，React 原生特性是由单个工程师编写的，而不是针对每个平台。从 React 本地工程师的角度来看，如果他们比在 Android 或 iOS 上多花 50%的时间来编写他们的功能，他们会觉得时间更长，即使总体上花的时间更少。

## 公共资源和文档

Android 和 iOS 已经有十年的历史，数百万工程师为学习资源、开源和在线帮助做出了贡献。我们利用许多资源，如 [CodePath](https://codepath.com/androidbootcamp) 来帮助人们学习 Android 和 iOS。尽管 React Native 拥有最大的跨平台社区之一，并且可以利用 React 资源，但它仍然比 Android 和 iOS 小得多。再加上我们必须在内部构建大部分基础设施，这意味着我们有限的 React 本地资源相对于本地资源而言被过度投资于教育和培训。

这是一系列博客文章的第三部分，重点介绍了我们使用 React Native 的体验以及 Airbnb 的下一步移动应用。

*   [第一部分:在 Airbnb 上反应本土](/airbnb-engineering/react-native-at-airbnb-f95aa460be1c)
*   [第二部分:技术](/airbnb-engineering/react-native-at-airbnb-the-technology-dafd0b43838)
*   [*第 3 部分:构建跨平台移动团队*](/airbnb-engineering/building-a-cross-platform-mobile-team-3e1837b40a88)
*   [第 4 部分:做出反应原生的决定](/airbnb-engineering/sunsetting-react-native-1868ba28e30a)
*   [第五部分:手机的下一步是什么](/airbnb-engineering/whats-next-for-mobile-at-airbnb-5e71618576ab)