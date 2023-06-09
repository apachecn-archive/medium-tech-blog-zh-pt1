# 不要相信这些关于性能调优的大谎言

> 原文：<https://medium.com/androiddevelopers/don-t-believe-these-big-fat-lies-about-performance-tuning-325d43aec90?source=collection_archive---------0----------------------->

![](img/b8fe89e70f64a14f0b86df9dbb7a6186.png)

去年，谷歌开发者关系(Google Developer Relations)和 VetNet 团队组织了一个为期五天的免费速成班，退伍军人及其配偶在很少或没有编程经验的情况下，学习如何构建 Android 应用程序。没错，这个班的学生以前从来没有编程过。

他们主要用 XML 编写应用程序，用一点点 Java 代码把它们联系在一起。他们想出的东西非常惊人:好看又有用的应用程序。在下图中，你可以看到他们的应用程序一般会显示一堆信息和几个图像，如果你点击一个按钮或图像，你会被带到一个有更详细信息的网站。

![](img/38d57e4970804d03a6c0f66192d7537e.png)

App credits: Brandon Spence — Awesome Day App, Home Vegetable Garden — Easter Jang, Dog CPR — Jimmy Dang

班级正在愉快地进行着，然后，在我们意识到之前，我们的第一个学生遇到了…一个明显可见的表现问题。第三天，牛逼日应用的作者打电话给助教求助，因为当他们滚动图像时，他们的一系列图像在屏幕上抖动和口吃。

通过解决这个学生的问题，我们发现了关于性能调优的四个广为接受的信念，而这四个信念都是不正确的。让我们来看看。

# 弥天大谎#1:性能调优只适用于专业人士

正如我们的故事所展示的，性能问题可能出现在每个体验层次。即使你正在编写你的第一个应用程序。

因为，作为初学者、业余爱好者、业余爱好者或临时开发人员，您很可能还没有开发出从上午 10 点到晚上 11:59 点编写代码的人的算法、模式和编码流畅性，这意味着，您很可能还没有一个高性能代码开发人员的习惯。

另一方面，经验丰富的开发人员有习惯，并不是所有的习惯都是好的。改变坏习惯比一开始就学习正确的习惯要难得多。因此，新开发人员有优势从一开始就这样做。

现在，初学者遇到的性能问题可能与专业人员遇到的问题大不相同……这就引出了第二个谎言。

# 弥天大谎 2:性能调优只适用于大型复杂应用

每一个应用程序都有巨大的潜力表现得非常糟糕。

现在，你可能会认为你的应用程序太简单、太小，不需要担心性能。然而，一个小型应用程序，例如，本地存储所有数据并使用许多视图来显示它，可能不如一个被迫使用更高级技术的大型应用程序性能好。

对你有利的是，小应用程序可能有明显的问题，你可以很快找到，并通过一个已知的好代码更改轻松修复。在大型复杂的应用程序中，专业人员首先会避免这些问题，相反，可能会有十几个小问题累积起来会给每帧的渲染增加 3 毫秒。筛选一百万行代码和调试日志，并嗅出需要进行计算的那个循环，需要知识和经验。

为了说明我的观点，Awesome Day 应用程序包括一个可点击图像的滚动列表。那个学生叫我过去，因为他们的应用程序滚动速度慢得令人难以置信。他们不知道该怎么办。毕竟，他们遵循了课程和官方文件中的说明。这个应用程序的总代码不到 200 行，由开发人员编写。它不只是小，它很小。所以，这应该很容易解决…这就把我们带到了第三个谎言。

# 弥天大谎#3:性能调整很难

好吧，原来牛逼的一天使用了原始的，巨大的图像挤压到可用的屏幕资产。

这是一个很容易找到的问题，通过猜测一个常见的初学者问题，然后查看参考资料，不需要任何工具。对于一个全新的开发者来说，这是一个很容易解释的问题。最后，这是一个非常简单的应用概念验证修复的问题。该解决方案不需要任何花哨的东西，只需要缩小图像以更接近他们设备的屏幕大小。

其他容易发现和修复的渲染问题，你可以习惯性地注意:[只画你看到的](/google-developers/draw-what-you-see-and-clip-the-e11-out-of-the-rest-6df58c47873e#.rqu14i1ay)，[防止你的视图层次爆炸](/google-developers/simplify-complex-view-hierarchies-5d358618b06f#.mewzeip4z)，把所有与向用户展示东西不直接相关的东西[从 UI 线程](https://www.youtube.com/watch?v=sId51btzn_A&list=PLWz5rJ2EKKc9CBxr3BVjPTPoDPLdPIFCE&index=12)移开。

但是要看到结果并确定一个直接的和可修复的原因并不总是那么明显。修复后,“棒极了的一天”只是稍微快了一点……这是我们的第四个也是最后一个谎言。

# 弥天大谎#4:性能调优很容易

Awesome Day 应用程序很好地说明了性能调优是一个迭代过程。你发现并解决了一个明显的问题，只是为了发现另一个被掩盖的更重要的问题。

由于 Awesome Day 如此之小，答案必须在 XML 中，它由一个包含多个以图像为背景的[文本视图](http://developer.android.com/reference/android/widget/TextView.html)的[滚动视图](http://developer.android.com/reference/android/widget/ScrollView.html)组成。即使对于这么小的应用程序，这也会引起问题。

作为一个例子，我编写了一个测试应用程序，将六个具有代表性的 44K 背景图像的 TextViews 打包到一个 ScrollView 中，应用程序很快就通过加载崩溃了。日志使用诸如“抛出内存错误”之类的词语来讲述这个故事，但是这个[内存监控工具](http://developer.android.com/tools/performance/memory-monitor/index.html)图告诉了一切。

![](img/1208d113d76b5d68e7d5934c704284b7.png)

用一个 [ListView](http://developer.android.com/reference/android/widget/ListView.html) 替换 ScrollView 最终解决了这个问题。

这也意味着学生必须学习 ListView 和适配器，这是一座相当陡峭的山。幸运的是，这就是助教和朋友的作用…还有 [stackoverflow](http://stackoverflow.com/) ，世界上每一个编码问题都在这里得到了解答。

因此，解决一些问题很容易，解决其他问题会让你成为更好的开发人员，因为这是一个学习新的、更好的方法来实现你梦想中的应用的机会。

所以，你有它。

真相就在那里

#性能问题

查看支持多屏幕的以了解更多关于选择多屏幕图像尺寸的信息。为了将你的 Android 开发技能提高到一个新的水平，请查看 Udacity 上的 [Android 性能课程](https://goo.gl/v6Eaec)和 YouTube 上的 [Android 性能模式](https://goo.gl/cnn9aF)。但最重要的是，加入我们的 [Android Performance G+社区](https://goo.gl/Nqfsx7)来获得关于构建高性能 Android 应用的伟大技巧，你猜对了。