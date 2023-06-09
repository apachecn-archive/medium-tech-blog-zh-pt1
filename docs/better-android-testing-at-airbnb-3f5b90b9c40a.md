# Airbnb 更好的 Android 测试——第 1 部分:哲学和嘲讽

> 原文：<https://medium.com/airbnb-engineering/better-android-testing-at-airbnb-3f5b90b9c40a?source=collection_archive---------0----------------------->

![](img/6b382f085c61217dfae09d185b523ed7.png)

我们在 Airbnb 建立的关于 Android 测试框架的七部分系列——第一部分着眼于我们的测试哲学，以及为我们所有测试奠定基础的模拟系统。

由于技术和组织方面的原因，Android 开发的自动化测试在 Airbnb 一直具有挑战性。相反，我们依赖手工测试在发布之前捕捉回归。虽然我们有成千上万的单元测试，但是这些测试通常是针对我们的基础设施和库的——我们的 UI 特性不适合自动测试。

我们已经试验过[浓缩咖啡](https://developer.android.com/training/testing/espresso)，但是发现它太易剥落，太费时间，不值得。这有几个原因:

*   很难模拟网络请求。我们尝试用 [OkReplay](https://github.com/airbnb/okreplay) 记录请求，但是发现维护最新 JSON 记录的开销很麻烦。
*   我们的片段将业务逻辑和 UI 代码结合在一起，因此很难模仿或隔离其中任何一个。
*   片状剥落使得测试不可靠，并减缓了开发人员的速度。
*   我们不断变化的应用程序的维护开销太高，而工程资源是有限的。
*   我们的应用程序的复杂性很大程度上来自于 UI 设计——很难用 Espresso 来测试。例如，断言适当的填充、文本颜色和其他样式是不切实际的。

所有这些创造了一个环境，在这个环境中，使用 QA 团队来捕捉问题比花费工程时间来维护测试更有意义。

多年来，这种方式一直运转良好，但随着我们的不断发展，这种方式开始失灵。我们的应用程序变得越来越复杂，有几十个贡献者，许多独立的团队，数百个屏幕，以及不断增长的业务需求。

很难可靠地捕捉每个版本中的回归。我们也无法开始使用 Espresso，因为所有之前的担忧仍然存在，并且会随着应用程序的增长而放大。很明显，我们需要在测试上认真投资，但是我们需要对我们的产品架构进行一个阶跃变化演化来实现它。

# 输入 MvRx

传统上，我们用片段构建 UI，将网络请求、数据存储和 UI 管理都集中在同一个类中。这方面的问题数不胜数，多年来一直是 Android 社区讨论的焦点。特别是对于测试来说，这是有问题的，因为我们没有一个可靠的方法来设置每个片段的字段值，所以模仿它们进行测试是乏味的。此外，如果片段中的某些内容改变了数据，我们无法防止或检测副作用。

这些问题导致了诸如 MVP、MVI 和 MVVM 这样的架构，它们分离了视图和业务逻辑。我们最初很慢地采用了其中的一个——担心引入开销以及在一个强制标准上排列几十个团队的困难——但是当 Android Jetpack ViewModel 发布时，我们认为它非常适合我们。

由于我们需要给我们的产品团队一个标准化的架构，我们在 Jetpack 的 ViewModel 之上建立了结构，[，我们在 2018 年发布了开源的 MvRx 库](/airbnb-engineering/introducing-mvrx-android-on-autopilot-552bca86bd0a)。

已经有很多关于 MvRx 的文章了(还有 Gabriel Peal 的[几个伟大的演讲)，所以我不会在这里深入讨论。然而，对于本文来说，有必要理解 MvRx 依赖于单个 State 类的原理，该类包含渲染屏幕所需的所有数据。这个状态是用一个 Kotlin 数据类实现的，它被强制为不可变的——状态只能通过提交一个新的状态实例来改变。MvRx 提供了方便设置状态和监听状态变化的工具。](https://www.youtube.com/watch?v=Web4xPi2Ga4)

MvRx 还与[环氧](https://github.com/airbnb/epoxy)很好地集成，用于以编程方式构建 UI；这是我们在 Airbnb 使用的模式。使用 Epoxy，我们的 MvRx 片段提供了一个“控制器”,它将当前状态作为输入，并输出要显示的视图和要绑定到它们的数据。

下面是一个简单屏幕所需的完整代码。

A “Hello World” example with MvRx and Epoxy

*注意:MvRx 与 Android 数据绑定和其他 UI 框架也能很好地工作，并且不依赖于 Epoxy。*

测试的重要之处在于，我们可以将我们想要的任何状态实例强制显示在屏幕上，并孤立地测试它，而不用担心数据最初来自哪里，也不用担心它是否会被副作用所改变。此外，我们可以确定片段中的 UI 仅仅是状态的一个函数，没有不可控制的副作用，这些副作用会导致测试中出现碎片。

每当状态改变时，UI 都被重新构建，并且完全基于状态。Epoxy 帮助我们用最少的样板文件高效地完成这项工作，但是任何你喜欢的 UI 框架都可以和 MvRx 一起使用，比如 Android 数据绑定。

MvRx 现已被我们所有的产品团队采用，并在 Airbnb 取得了巨大的成功。使用 MvRx，我们可以在更短的时间内构建新的特性，同时使它们更易维护、更健壮、更高性能，是的，更易测试。

# 我们的测试愿景

一旦 Airbnb 的团队采用了 MvRx，我们就开始考虑如何更好地支持它的测试。虽然我们可以让我们的工程师为他们的功能编写 Espresso 测试，但我们之前讨论的 Espresso 的潜在剥落可能会破坏 CI。因此，工程师将不得不花费大量的时间来维护他们的测试。

相反，我们希望建立一个测试框架，用最少的工作提供高测试覆盖率。我们调查了可能的测试解决方案，并为我们在测试框架中想要的东西提出了一些指导原则。在我们的理想世界里:

*   测试应该是稳定的，没有剥落
*   向屏幕添加测试应该很容易，更新测试应该很简单
*   测试应该在 CI 上快速运行
*   测试框架应该支持 100%的代码覆盖率
*   测试应该通过快速验证变更来加快开发

这些理想在理论上是伟大的，但是经常会有妥协，因为如果不花费过多的时间编写和维护测试，很难有好的测试覆盖率。然而，我们希望能提供比香草浓缩咖啡更好的产品，所以我们开始思考我们能做些什么来实现我们的愿景。

# MvRx 片段模拟

我们在 MvRx 模拟系统上结盟，其中片段为它们使用的任何视图模型定义了状态的模拟实现。每个屏幕都有一个规范的“默认”状态表示，默认状态的“变体”描述了我们想要测试的其他 UI 变体。我们所有的测试都建立在这个模拟框架之上。

这里有一个我们的 MvRx 示例应用程序的模拟版本的例子。该页面显示了使用分页异步加载的笑话列表。

![](img/b7028ff0d6b5f4a973f91cb3dc10dfca.png)

MvRx sample app showing a list of jokes

我们通过覆盖 ***provideMocks*** 函数直接在片段中注册 mocks。

这将声明强制 ViewModel 进入哪个状态，如果片段需要，还可以指定模拟参数。ViewModel 通常如何填充状态并不重要，无论是通过网络请求、数据库查询还是其他方式。使用我们的模拟框架，ViewModel 被冻结在一个不可更改的状态。该片段接收该状态，并呈现一致的 UI 以便于测试。由于 MvRx 使用 Kotlin 数据类来实现状态，模拟一个片段所需的唯一工作就是实现其状态的完整版本。

我们在一个单独的文件中定义了模拟状态，以便于管理。

对于复杂的屏幕，这可能是一个很大的数据类，手动创建会很乏味。相反，我们创造了一种轻松生成它的方法:

1.  在运行时的调试版本中，MvRx 片段注册一个广播接收器，该接收器监听某个意图动作。
2.  开发人员从命令行运行一个 Kotlin 脚本，该脚本通过 adb 发出带有该动作的广播。
3.  广播接收器激活并反射性地收集片段上的所有视图模型。
4.  每个视图模型的状态被反射性地分析，检索主构造函数参数的值并生成重建它们所需的代码。对于嵌套对象，这将递归地继续下去，从而生成可以完全重构状态类的代码。
5.  每个视图模型的代码都保存在设备上的 kt 文件中，脚本将它们从设备中拖到应用程序中的 mocks 包中

要用这些模拟数据运行测试，开发人员只需在他们的设备上打开一个屏幕，运行一个命令。生成模拟并复制到它们的源文件中，然后只添加上面显示的几行代码片段，我们就完成了。为复杂的屏幕设置一个完整的模拟只需要几分钟，这为整个屏幕增加了全面的测试覆盖面(在以后的文章中，我们将讨论使用这个模拟数据运行什么测试)。

# 模拟变体

由于我们使用不可变的 Kotlin 数据类，主要的默认 mock 可以在共享一个 ViewModel 的所有片段之间重用，从而减少了为一个流设置 mock 所需的时间。

如果一个状态被改变了，为了更新整个流程的测试，它的模拟只需要在一个地方被修改。我们还有编译时保证，如果状态模式被修改，我们的模拟数据保持最新。

如果共享 mock 的片段需要任何不同的默认状态，它们可以利用数据类的复制构造函数。此外，对于一个屏幕来说，需要测试的状态变化是很常见的。这些状态变化也可以利用默认状态和复制构造函数。

为了简化修改默认状态的过程，我们创建了一个 DSL 来定义状态变量。看起来是这样的:

使用这个 DSL，每个变体都有一个名称和一个模拟状态。这段代码创建了三个模拟:

*   一个名为“默认状态”的主模拟，使用主参数中给定的默认状态对象
*   一种“加载”变体，其中初始请求仍在加载数据
*   “空结果”模拟，返回的数据为空

这个 DSL 对于改变嵌套对象上的数据特别方便。通常当使用数据类的复制函数时，复制嵌套了几层的单个值是很麻烦的。

这个新语法从默认状态开始，并使用反射来指定嵌套越来越多的层。指定的最后一个对象是被更改的对象。属性嵌套越多，这就越有帮助。

虽然可以通过这种语法将值设置为任何值，但是帮助器使得更改常见的原始值变得很容易。让我们来看一个更复杂的预订页面的状态模拟:

对于其他常见的值，如空、零和假，也存在快捷方式。

如果需要改变多个值，那么这些设置器就被链接起来。但是，我们建议将每个模拟限制为只有一个更改的值，以限制它测试的范围。

lambda 的返回值被用作变体的最终模拟状态。

如果片段有参数，那么这些参数也可以改变。这让您可以测试传递给片段用于初始化的不同参数。

总的来说，这种语法使得测试边缘案例的过程非常简单。每个变体代表一个不同的测试用例，允许完全覆盖屏幕。

# 模拟发射器

特性开发的基本模式是编写一些代码，运行它，检查它是否如你所愿，然后令人厌烦地重复。对于一个应用程序，你通常会重新启动进入活动，可能需要花一些时间点击嵌套的屏幕，以获得你实际改变的功能，这可能是乏味的。

我们建立了一个启动器系统来避免这种痛苦，并允许我们的开发人员直接跳到应用程序中的任何屏幕，具有他们需要的任何启动状态。

![](img/837f6e6fd46df31e05b65b345524b2c4.png)

MvRx Launcher main screen example

这个启动器屏幕是我们的“开发”应用程序的入口活动。该活动自动检测应用程序的 dex 文件中的片段，检索它们的模拟，并在屏幕上列出它们。

![](img/cf8a0fb80a6021b0678830b4c57405a2.png)

MvRx Launcher mock variant selection for a Fragment

单击一个片段会显示为该片段定义的所有模拟变体，选择一个模拟会立即加载它。

![](img/c50cda6979a460b039dda7e87e4a6cf9.png)

这使得跳转到应用程序中的任何屏幕(或该屏幕的边缘情况)变得简单，即使它通常在流的深处。

这些开发应用在技术上是 Android 风格的，只包括开发人员想要测试的功能模块。这些风格利用了我们的应用程序的模块化架构，并允许更快的构建时间。

为了进一步提高开发人员的迭代速度，启动器会记住您最后选择的模拟，并在下次启动时自动重新打开它。它还支持深层链接，因此任何屏幕或模拟状态都可以从命令行按名称打开。模拟选择允许您轻松地测试您的屏幕可能遇到的每个状态和边缘情况，从而在提交更改之前轻松地进行本地测试。

# 开放源码

这个模仿系统和启动器被设计成干净地集成到 MvRx 库中。我们最初在我们的内部应用程序中构建它们，但后来从 2.0.0 alpha 版本开始，已经将它们移动到开源的 MvRx 存储库中。

我们将继续开发这项开源工作，以确保 MvRx 是一个构建应用程序的优秀架构。

# 下一步:自动截图测试

在本文中，我们展示了我们在 MvRx 上构建的 mocking 框架，该框架支持在 Airbnb 上对 Android 进行全面测试。我们还向您展示了 MvRx 启动器，这是我们在 mocking 框架之上构建的工具之一，用于帮助开发人员测试他们的代码。

在[第 2 部分](/airbnb-engineering/better-android-testing-at-airbnb-a77ac9531cab)中，我们将讨论这个模拟系统如何用于自动化屏幕截图测试和捕捉 CI 上的 UI 回归。

## 系列概述

这是关于 Airbnb 测试的七篇系列文章。每周都会发布一个新的部分。

**第一部分(本文)**——[测试哲学和模拟系统](/airbnb-engineering/better-android-testing-at-airbnb-3f5b90b9c40a)

第 2 部分— [使用 MvRx 和 Happo 进行截图测试](/airbnb-engineering/better-android-testing-at-airbnb-a77ac9531cab)

第 3 部分— [自动化交互测试](/airbnb-engineering/better-android-testing-at-airbnb-1d1e91e489b4)

第 4 部分— [单元测试框架视图模型](/airbnb-engineering/better-android-testing-at-airbnb-part-4-testing-viewmodels-550d929126c8)

第 5 部分— [我们的自动化测试框架的架构](/airbnb-engineering/better-android-testing-at-airbnb-661a554a8c8b)

第 6 部分— [持续嘲讽的障碍](/airbnb-engineering/better-android-testing-at-airbnb-a11f6832773f)

第 7 部分— [测试生成和 CI 配置](/airbnb-engineering/better-android-testing-at-airbnb-eacec3a8a72f)

## 我们在招人！

想和我们一起在这些和其他大规模的 Android 项目上合作吗？Airbnb 正在全公司招聘几个 Android 工程师职位！有关当前空缺，请参见[https://careers.airbnb.com](https://careers.airbnb.com/)。