# Android 上的协同程序(第三部分):实际工作

> 原文：<https://medium.com/androiddevelopers/coroutines-on-android-part-iii-real-work-2ba8a2ec2f45?source=collection_archive---------1----------------------->

![](img/a0be03964836145a1dc72cbfd017a28d.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

这是关于在 Android 上使用协程的系列文章的一部分。这篇文章主要通过实现一次请求来解决使用协程的实际问题。

## 本系列的其他文章:

[](/androiddevelopers/coroutines-on-android-part-i-getting-the-background-3e0e54d20bb) [## Android 上的协同程序(第一部分):了解背景

### 协程解决什么问题？

medium.com](/androiddevelopers/coroutines-on-android-part-i-getting-the-background-3e0e54d20bb) [](/androiddevelopers/coroutines-on-android-part-ii-getting-started-3bff117176dd) [## Android 上的协同程序(第二部分):入门

### 了解 CoroutineScope 以及它如何帮助您避免在 Android 上泄露工作。

medium.com](/androiddevelopers/coroutines-on-android-part-ii-getting-started-3bff117176dd) 

# 用协程解决现实世界的问题

本系列的第一部分和第二部分主要关注如何使用协程来简化代码，在 Android 上提供主安全，并避免泄漏工作。在这种背景下，它们看起来像是后台处理的一个很好的解决方案，也是简化 Android 上基于回调的代码的一种方式。

到目前为止，我们已经关注了什么是协程以及如何管理它们。在这篇文章中，我们将看看如何使用它们来完成一些真正的任务。协程是一种通用编程语言特性，与函数处于同一层次——所以您可以用它们来实现任何您可以用函数和对象实现的东西。然而，有两种类型的任务在实际代码中经常出现，协程是解决这两种任务的好方法:

1.  **One shot** **请求**是每次被调用时运行的请求——它们总是在结果准备好之后完成。
2.  **流** **请求**是持续观察变化并将其报告给调用者的请求——它们不会在第一个结果准备好时完成。

协程是这两项任务的绝佳解决方案。在本帖中，我们将深入探讨一次性请求，并探索如何在 Android 上使用协程来实现它们。

# 一次性请求

单次请求在每次调用时执行一次，并在结果准备就绪后立即完成。这种模式与常规的函数调用相同——它被调用，做一些工作，然后返回。由于与函数调用的相似性，它们比流请求更容易理解。

> 每次调用时都会执行一次单次请求。一旦结果准备好，它就停止执行。

对于一个一次性请求的例子，考虑你的浏览器是如何加载这个页面的。当你点击这篇文章的链接时，你的浏览器向服务器发送了一个网络请求来加载这个页面。一旦页面被传输到你的浏览器，它就停止与后端对话——它有所有它需要的数据。如果服务器修改了帖子，新的更改将不会显示在您的浏览器中，您必须刷新页面。

因此，虽然它们缺乏流请求的实时推送，但一次性请求非常强大。在 Android 应用程序中，你可以做很多事情，这些事情可以通过一次性请求来解决，比如获取、存储或更新数据。对于排序列表之类的事情，这也是一个很好的模式。

# 问题:显示排序列表

让我们通过查看如何显示一个排序列表来研究一次性请求。为了具体说明这个例子，让我们构建一个库存应用程序，供商店的员工使用。它将被用来查找产品的基础上，当他们最后一次库存-他们将希望能够排序列表升序和降序。它有如此多的产品，以至于对其进行排序可能需要一秒钟——所以我们将使用协程来避免阻塞主线程！

在这个应用程序中，所有产品都存储在一个房间数据库中。这是一个很好的探索用例，因为它不需要涉及网络请求，所以我们可以专注于模式。尽管这个例子因为不使用网络而更简单，但它公开了实现一次性请求所需的模式。

为了使用协程实现这个请求，您将向`ViewModel`、`Repository`和`Dao`引入协程。让我们一次浏览一遍，看看如何将它们与协程集成。

`ProductsViewModel`负责从 UI 层接收事件，然后向存储库请求更新的数据。它使用`[LiveData](https://developer.android.com/topic/libraries/architecture/livedata)`来保存当前排序的列表，以供 UI 显示。当新事件到来时，`sortProductsBy`启动一个新的协程来对列表进行排序，并在结果就绪时更新`LiveData`。在这个架构中，`[ViewModel](https://developer.android.com/topic/libraries/architecture/viewmodel)`通常是启动大多数协程的正确位置，因为它可以取消`onCleared`中的协程。如果用户离开屏幕，他们通常对杰出的工作没有用处。

*如果你没怎么用过 LiveData，看看这篇由*[*@ CeruleanOtter*](https://twitter.com/CeruleanOtter)*撰写的伟大文章，介绍他们如何为 ui 存储数据。*

[](/androiddevelopers/viewmodels-a-simple-example-ed5ac416317e) [## 视图模型:简单的例子

### 介绍

medium.com](/androiddevelopers/viewmodels-a-simple-example-ed5ac416317e) 

这是 Android 上协程的一般模式。由于 Android 框架不调用挂起函数，您需要与协程协调来响应 UI 事件。最简单的方法是在事件到来时启动一个新的协程——这样做的自然位置是在`ViewModel`中。

> 作为一般模式，在 ViewModel 中启动协程。

`ViewModel`使用一个`ProductsRepository`来实际获取数据。看起来是这样的:

`ProductsRepository`提供了与产品交互的合理界面。在这个应用程序中，由于所有东西都在本地房间数据库中，它只是为`@Dao`提供了一个很好的接口，该接口针对不同的排序顺序提供了两种不同的功能。

存储库是 Android Architecture Components 架构的可选部分——但如果你的应用程序中有它或类似的层，它应该更喜欢公开常规的挂起功能。由于存储库没有自然的生命周期——它只是一个对象——它没有办法清理工作。因此，默认情况下，在存储库中启动的任何协程都会泄漏。

除了避免泄漏之外，通过公开常规的挂起函数，很容易在不同的上下文中重用存储库。任何知道如何做协程的人都可以叫`loadSortedProducts`。例如，由 WorkManager 库调度的后台作业可以直接调用它。

> 存储库应该更喜欢公开主安全的常规挂起函数。

> 注意:*一些后台保存操作可能希望在用户离开屏幕后继续，并且让这些保存没有生命周期地运行是有意义的。在大多数其他情况下，* `*viewModelScope*` *是合理的选择。*

继续看`ProductsDao`，看起来是这样的:

`ProductsDao`是一个暴露两个挂起函数的房间`[@Dao](https://developer.android.com/reference/android/arch/persistence/room/Dao)`。因为这些函数被标记为 suspend，所以 Room 确保它们是主安全的。这意味着您可以从`Dispatchers.Main`直接调用它们。

*如果你还没有在房间里看到 coroutines，看看这个由*[*@*FMuntenescu](https://twitter.com/FMuntenescu)发布的伟大帖子

[](/androiddevelopers/room-coroutines-422b786dc4c5) [## 房间🔗协同程序

### 给你的数据库增加一些悬念

medium.com](/androiddevelopers/room-coroutines-422b786dc4c5) 

不过需要提醒的是，调用这个函数的协程将在主线程上。因此，如果您对结果做了一些开销很大的事情——比如将它们转换成一个新的列表——您应该确保没有阻塞主线程。

> *注意:* *Room 使用自己的 dispatcher 在后台线程上运行查询。您的代码*不应该*使用* `*withContext(Dispatchers.IO)*` *来调用暂停房间查询。这将使代码变得复杂，并使您的查询运行得更慢。*

> Room 中的挂起功能是主安全的，并在自定义调度程序上运行。

# 一次性请求模式

这是在 Android 架构组件中使用协程进行一次性请求的完整模式。我们向`ViewModel`、`Repository`和`Room`添加了协程，每一层都有不同的职责。

1.  ViewModel 在主线程上启动一个协程——它在有结果时完成。
2.  Repository 公开了常规的挂起函数，并确保它们是主安全的。
3.  数据库和网络公开常规挂起函数，并确保它们是主安全的。

**`**ViewModel**`负责启动协程，并确保当用户离开屏幕时它们被取消。它不做昂贵的事情，而是依靠其他层来做繁重的工作。一旦有了结果，它就使用`LiveData`将结果发送到 UI。**

**由于`ViewModel`不做繁重的工作，它在主线程上启动协程。通过在 main 上启动，如果结果立即可用(例如，从内存缓存中)，它可以更快地响应用户事件。**

****`**Repository**`公开常规的挂起函数来访问数据。它通常不会启动自己的长期协程，因为它没有办法取消它们。每当`Repository`必须做一些昂贵的事情，比如转换一个列表，它应该使用`withContext`来公开一个主安全接口。****

******数据层**(网络或数据库)总是暴露常规的挂起功能。使用 Kotlin 协程时，这些挂起函数是主安全的，这一点很重要，房间和翻新都遵循这一模式。****

****在单次请求中，数据层*仅*暴露挂起功能。如果调用者想要一个新的值，他们必须再次调用。这就像网页浏览器上的刷新按钮。****

****值得花点时间来确保您理解这些一次性请求的模式。这是 Android 上协程的正常模式，您会一直使用它。****

# ****我们的第一份错误报告！****

****测试该解决方案后，您将其投入生产，几周以来一切都很顺利，直到您收到一个非常奇怪的错误报告:****

> ******主题:**🐞—错误的排序顺序！****
> 
> ******报告:**当我非常非常快速地点击排序按钮时，有时排序是错误的。这种情况并不经常发生🙃。****

****你看一看，挠一挠头。会出什么问题呢？算法似乎相当简单:****

1.  ****开始用户请求的排序。****
2.  ****在房间调度程序中运行排序。****
3.  ****显示排序的结果。****

****你很想关闭 bug**“wont fix——不要这么快按下按钮”**但是你担心可能有什么东西坏了。在添加了日志记录语句并编写了一个测试来一次调用大量排序之后——您终于弄明白了！****

****原来显示的结果实际上并不是“第*次排序的结果”，而是“第*次排序的结果”当用户按下按钮时——他们同时开始多个排序，并且可以按任何顺序完成！******

> ****当启动一个新的协同例程来响应一个 UI 事件时，考虑一下如果用户在这个完成之前启动另一个会发生什么。****

****这是一个*并发错误*，它与协程没有任何关系。如果我们以同样的方式使用回调、Rx，甚至是`ExecutorService`,我们也会有同样的错误。****

****在`ViewModel`和`Repository`中有很多方法可以解决这个问题。让我们探索一些模式来确保单次请求按照用户期望的顺序完成。****

# ****最佳解决方案:禁用按钮****

****最根本的问题是我们在做两种分类。我们可以通过让它只做一种来解决这个问题！最简单的方法是禁用排序按钮来停止新事件。****

****这似乎是一个简单的解决方案，但它确实是一个好主意。实现这一点的代码很简单，易于测试，只要它在 UI 中有意义，就可以完全解决问题！****

****要禁用按钮，告诉 UI 在`sortPricesBy`中发生了一个排序请求，如下所示:****

****Disabling the buttons while a sort runs using _sortButtonsEnabled in sortPricesBy.****

****好的，那个不算太坏。只需禁用调用存储库周围的`sortPricesBy` 中的按钮。****

****在大多数情况下，这是解决这个问题的正确方法。但是，如果我们想让按钮保持启用状态并修复错误呢？这有点难，我们将在这篇文章的剩余部分探索一些不同的选择。****

> ****重要提示:*这段代码展示了在 main 上启动的一个主要优点——按钮在响应点击时会立即失效。如果你换了调度程序，一个在慢速手机上快速操作的用户可以发送不止一次点击！*****

# ****并发模式****

****接下来的几节将探讨高级主题——如果您刚刚开始使用协程，您不需要马上理解它们。简单地禁用按钮是解决您遇到的大多数问题的最佳方案。****

****在本文的其余部分，我们将探索使用协程来保持按钮启用的方法，但要确保一次性请求的执行顺序不会让用户感到意外。我们可以通过控制协程何时运行(或不运行)来避免意外并发。****

****有三种基本模式可用于一次性请求，以确保一次只运行一个请求。****

1.  ******开始更多工作前，取消之前的工作**。****
2.  ******将下一个工作**排入队列，等待前面的请求完成后再开始下一个。****
3.  ******加入之前的工作**如果已经有一个请求在运行，只需返回那个请求，而不是启动另一个请求。****

****当您浏览这些解决方案时，您会注意到它们的实现有些复杂。为了专注于如何使用这些模式而不是实现细节，我[创建了一个要点，将所有三个模式的实现](https://gist.github.com/objcode/7ab4e7b1df8acd88696cb0ccecad16f7#file-concurrencyhelpers-kt-L19)作为可重用的抽象。****

## ****解决方案 1:取消之前的工作****

****在排序的情况下，从用户那里获得一个新事件通常意味着您可以取消上一次排序。毕竟，如果用户已经告诉你他们不想要这个结果，那么继续下去还有什么意义呢？****

****要取消之前的请求，我们需要以某种方式跟踪它。要点中的函数`cancelPreviousThenRun` [正是这么做的。](https://gist.github.com/objcode/7ab4e7b1df8acd88696cb0ccecad16f7#file-concurrencyhelpers-kt-L91)****

****让我们来看看如何用它来修复这个错误:****

****Using cancelPreviousThenRun to ensure that only one sort runs at a time.****

****查看 gist 中`cancelPreviousThenRun`的[示例实现](https://gist.github.com/objcode/7ab4e7b1df8acd88696cb0ccecad16f7#file-concurrencyhelpers-kt-L91)是了解如何跟踪正在进行的工作的好方法。****

****简而言之，它总是跟踪成员变量`activeTask`中当前活动的排序。每当排序开始时，它会立即`[cancelAndJoin](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/cancel-and-join.html)`处理当前在`activeTask`中的任何东西。这具有在开始新的排序之前取消任何正在进行的排序的效果。****

****使用类似于`ControlledRunner<T>`的抽象来封装这样的逻辑是一个好主意，而不是将临时并发与应用程序逻辑混合。****

> ****考虑构建抽象，以避免将临时并发模式与应用程序代码混合在一起。****

> ****重要提示:*这种模式不太适合在全局单例中使用，因为不相关的调用者不应该互相取消。*****

## ****解决方案 2:将下一个工作排队****

****有一种解决并发错误的方法*总是有效。*****

****将请求排队，这样一次只能发生一件事！就像商店里的队列或队伍一样，请求将按照开始的顺序一次执行一个。****

****对于这个特殊的排序问题，取消可能比排队更好，但是值得讨论一下，因为它总是有效的。****

****每当一个新的排序进来时，它使用一个`SingleRunner`实例来确保一次只有一个排序在运行。****

****它[使用一个](https://gist.github.com/objcode/7ab4e7b1df8acd88696cb0ccecad16f7#file-concurrencyhelpers-kt-L49) `[Mutex](https://gist.github.com/objcode/7ab4e7b1df8acd88696cb0ccecad16f7#file-concurrencyhelpers-kt-L49)`，这是一个单一的票(或锁)，协程必须得到它才能进入该块。如果另一个协程在一个正在运行时尝试，它会挂起自己，直到所有未决的协程都使用`[Mutex](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.sync/-mutex/)`完成。****

> ****互斥体让你确保一次只有一个协程运行——并且它们将按照开始的顺序完成。****

## ****解决方案 3:加入以前的工作****

****第三个要考虑的方案是加入之前的工作。如果新的请求将重新开始已经完成一半的完全相同的工作，这是一个好主意。****

****这种模式对于 sort 函数没有太大意义，但是对于加载数据的网络获取来说，这是一种自然的选择。****

****对于我们的产品库存应用程序，用户需要一种从服务器获取新产品库存的方法。作为一个简单的 UI，我们将为他们提供一个刷新按钮，他们可以按下这个按钮来启动一个新的网络请求。****

****就像排序按钮一样，在请求运行时简单地禁用按钮是这个问题的完整解决方案。但是，如果我们没有——或者不能——这样做，我们可以加入现有的请求。****

****让我们来看看使用 gist 中的 [joinPreviousOrRun](https://gist.github.com/objcode/7ab4e7b1df8acd88696cb0ccecad16f7#file-concurrencyhelpers-kt-L124) 的一些代码，看看它是如何工作的:****

****这颠倒了`cancelPreviousAndRun`的行为。它不是通过取消请求来丢弃以前的请求，而是丢弃新的请求并避免运行它。如果已经有一个请求在运行，它会等待当前“运行中”请求的结果并返回，而不是运行一个新的请求。只有在没有请求运行的情况下，才会执行该块。****

****你可以在`joinPreviousOrRun`开始时看到它是如何工作的——如果`activeTask`中有任何东西，它就返回先前的结果:****

****这种模式很适合于像通过`id`获取产品这样的请求。您可以添加一个从`id`到`Deferred`的映射，然后使用相同的连接逻辑来跟踪相同产品的先前请求。****

> ****加入以前的工作是避免重复网络请求的一个很好的解决方案。****

# ****下一步是什么？****

****在这篇文章中，我们探讨了如何使用 Kotlin 协程实现一次请求。首先，我们实现了一个完整的模式，展示了如何在`ViewModel`中启动一个协程，然后从`Repository`和`Dao`空间中公开常规的挂起函数。****

****对于大多数任务来说，为了在 Android 上使用 Kotlin 协程，这是您需要做的全部工作。这种模式可以应用于许多常见的任务，比如像我们在这里展示的那样对列表进行排序。您还可以使用它来获取、保存或更新网络上的数据****

****然后，我们看了一个可能出现的细微错误和可能的解决方案。解决这个问题最简单(通常也是最好)的方法是在 UI 中——在排序过程中禁用排序按钮。****

****最后，我们看了一些高级并发模式以及如何在 Kotlin 协程中实现它们。这个的[代码有点复杂，但是它为一些高级协程主题提供了很好的介绍。](https://gist.github.com/objcode/7ab4e7b1df8acd88696cb0ccecad16f7#file-concurrencyhelpers-kt-L158)****

****在下一篇文章中，我们将看看流请求，并探索如何使用`liveData` builder！****