# 以实用的方式开始机器学习

> 原文：<https://medium.com/capital-one-tech/getting-started-with-machine-learning-the-pragmatic-way-749d6d2a2d43?source=collection_archive---------2----------------------->

![](img/af8fb798016720fec869e36303621ce5.png)

随着机器学习被用来解决越来越多有趣但越来越具挑战性的问题，对能够开发机器学习应用程序的人的需求也在不断增加。不同行业的公司都在招募机器学习专家；导致在可预见的未来，这类人才在就业市场上严重短缺。幸运的是，机器学习不再只是专家的专利，你不需要有博士学位就可以学习它。采取务实的方法，一名普通的软件工程师可以在相对较短的时间内学会成为一名有贡献的机器学习工程师。

# 我是如何进入机器学习的

在过去的三个月里，我参与了一个项目，将一些最新的机器学习技术应用于改善拍照文档的文本识别。与传统的光学字符识别(OCR)技术不同，传统的光学字符识别技术最适合平面扫描的文档，机器学习有可能为更复杂的场景找到解决方案，例如照明条件差、折叠的文档、文本上的插入线条等。对于我们的项目，我们采用了一些复杂的深度学习算法(深度学习是更广泛的机器学习方法家族的一部分)，包括卷积神经网络(CNN)和递归神经网络(RNN)，并能够在三个月的期末展示非常令人满意的结果。

经过学术界和工业界多年的努力，机器学习的准入门槛从未如此之低。你可以找到许多可以帮助你更容易进入这一领域的资源，包括免费的在线课程和书籍，实现几乎所有知名机器学习算法的开源项目，各种公共训练数据集，以及廉价而灵活的云计算基础设施。回顾我在这个项目上的学习经历，我相信大多数软件工程师都可以学习机器学习，并为他们的机器学习项目做出重大贡献。在这篇文章中，我将分享我个人的经验，如何实现这一目标。

# 弄脏你的手

像学习任何其他工程学科一样，开始机器学习的最佳方式是通过建立一个环境和尝试一些现有的机器学习应用程序来弄脏你的手。这不仅有助于你以后学习机器学习理论，也有助于你发展一些机器学习直觉，这在你开始从事实际项目时是极其重要的。

## 语言和库

首先，你应该安装并学习 Python(恭喜你已经是 Python 开发者了)，这是机器学习最流行的语言。人们确实在该领域使用其他语言，如 Java、R、JavaScript、C++、Julia、Scala、Lua 等，但作为机器学习的初学者，你会发现更多用 Python 编写的机器学习项目和示例。

***提示:如果你在 Mac OS 上工作，又是 Python 新手，可能要安装***[***virtualenv***](https://virtualenv.pypa.io/en/stable/)***，这是一个与其他 Python 开发隔离的虚拟 Python 环境。这将为您节省大量时间来尝试对抗 Mac OS，这可能会阻止您升级许多机器学习库所需的一些 Python 包。***

如果你计划使用 Python，你需要安装 [NumPy 包](http://www.numpy.org/)并学习它的用法。NumPy 是科学计算的一个基础包，支持许多机器学习库。它让你使用一些非常简洁的语法来表达复杂的矩阵计算，这在机器学习算法中很常见。对 R 或 MatLab 有经验的人会发现和 NumPy 的相似之处。

***提示:有很多很棒的 Python 和 NumPy 教程和书籍。作为一个 Python 新手，我花了一下午的时间通读了***[***Python Numpy 教程***](http://cs231n.github.io/python-numpy-tutorial/) ***出自热门*** [***斯坦福 CS231n 在线课程***](http://cs231n.stanford.edu/) ***。***

我也强烈推荐安装 [Jupyter 笔记本](http://jupyter.org/)。这是一个 Web 应用程序，允许您交互式地编写和执行 Python 代码，并可视化执行结果。在机器学习中，人们通常会花大量时间来试验不同的算法参数，并观察它们如何影响结果。Jupyter Notebook 非常适合这种互动实验需求。

最后，您应该安装并学习使用一些机器学习库，以避免从头开始构建一切。在这方面有许多很好的选择。 [Scikit-learn](http://scikit-learn.org/) 可能是机器学习最流行的切入点，有很多现成的算法，比如 SVM、随机森林、逻辑回归等。对于深度学习， [TensorFlow](http://tensorflow.org/) 提供了许多支持 GPU 的低级构建模块，尤其擅长大规模分布式机器学习。作为一名机器学习初学者，您可能希望选择一个适合您的项目、具有大型社区和良好文档的库，以便在遇到问题时可以轻松寻求帮助。

## 绘图处理器

当运行涉及大量并行计算的机器学习算法时，GPU 是您的好朋友。对于这样的计算，GPU 通常比 CPU 提供更好的性能。

对于一些好的 GPU，无论是本地的还是基于云的，你都需要安装它们的驱动程序和支持 GPU 的库，以便你的机器学习算法可以在 GPU 上运行。比如你的 GPU 兼容 CUDA，你就要同时安装 [CUDA](http://www.geforce.com/hardware/technology/cuda) 和[cud nn](https://developer.nvidia.com/cudnn)；如果你打算使用 TensorFlow，你应该安装它的支持 GPU 的版本[。](http://www.nvidia.com/object/gpu-accelerated-applications-tensorflow-installation.html)

# 运行第一个示例

一旦你有了一个包含 Python、Numpy 和合适的机器学习库的环境，你就可以开始运行一些令人兴奋的机器学习应用程序了。如果你使用 TensorFlow，我会推荐你从机器学习的 [MNIST 例子](https://www.tensorflow.org/get_started/mnist/beginners)、T2【Hello World】开始。该示例基本上读取了 [MNIST](http://yann.lecun.com/exdb/mnist/) ，一个手写数字的数据库，使用该数据训练一个非常简单的分类算法，目标是从图像中识别一个数字，并测试该算法以找出其分类准确性。最初，您可能会被示例教程中使用的数学和术语吓到。但是，您可以暂时跳过它们，专注于让示例在您的环境中运行。在这个例子之后，你可以尝试 MNIST 的[深度学习版本，或者切换到其他更有趣的例子。](https://www.tensorflow.org/get_started/mnist/pros)

在这个阶段，您不需要理解示例代码。本练习的主要目标是让自己熟悉机器学习应用程序所需的典型工具、环境和工作流。希望在尝试了一些例子后，你会有动力去学习这些例子背后的理论。

# 学习理论

设计新算法或理解现有算法需要一些机器学习专业知识。由于机器学习是一个非常广泛的主题，为了更好地利用你的时间，你应该首先学习一些机器学习的基础知识，然后专注于与你的项目相关的主题。为了使理论学习更有效，你应该利用你刚刚建立的环境来练习你所学的东西。

# 数学先决条件

理解机器学习理论有一些先决条件，包括线性代数，统计和概率，以及微积分。你不需要全部掌握——快速回顾一下相关部分就足够了。如果时间有限或者你对这些科目不熟悉，我建议你熟悉基本的矩阵计算，包括矩阵维数、矩阵加法和乘法等。，因为稍后在实现算法时会用到它们。

# 心态

我建议至少上一门关于机器学习的入门课程。这将让你对这个主题有一个全局的了解，这样你就可以对机器学习可以做什么以及你应该为你的项目关注哪个方向有一些粗略的想法。这也将帮助你建立你的机器学习词汇，以便更好地与该领域的其他人交流。

在诸如 Coursera、T2、优达城、T4 等地有许多免费的在线课程。我个人花了整整一周的时间参加了吴恩达教授的课程，发现它非常有帮助。虽然该课程不包含最新的机器学习算法，但它在广度和深度之间取得了很好的平衡。本课程的很大一部分解释了机器学习背后的数学原理——如果你很难掌握它们，并且专注于理解一般概念和技术，跳过它们是完全可以的。

如果你更喜欢通过看书来学习，也有很多[免费的机器学习书籍](https://github.com/josephmisiti/awesome-machine-learning/blob/master/books.md)可供选择。比如[深度学习](http://www.deeplearningbook.org/)这本书，由该领域的三位专家撰写，既有数学背景，也有行业内从业者使用的深度学习技术；[统计学习简介](http://www-bcf.usc.edu/~gareth/ISL/)另一本获奖的机器学习书籍，非常全面的介绍了统计学习方法。

# 发展更具体的专业知识

现在，您已经有了一些关于机器学习的基础知识和工作经验，您可以通过咨询机器学习专家、阅读最新论文和评估各种开源项目等，更深入地研究与您的项目最相关的机器学习主题。

对于 OCR 项目，我的团队能够在街景中的[门牌号识别中识别出一个相关的问题，Google 使用 CNN(卷积神经网络)算法解决了这个问题。在学习了机器学习的基础知识并确定了我需要深入研究的具体主题后，我参加了关于 CNN 的](https://arxiv.org/pdf/1312.6082.pdf)[斯坦福在线课程](http://cs231n.github.io/)(如果你的问题可以通过 CNN 解决，我强烈推荐该课程)，并阅读了大量关于最先进的 CNN 算法的论文。这些知识帮助我更好地理解了 CNN 算法，以及我们如何为我们的项目调整它们。

***提示:如果你不确定应该选择哪些主题，这是向机器学习专家咨询或寻找该领域导师的绝佳时机。去你当地的机器学习聚会，参加*** [***会议***](http://www.kdnuggets.com/meetings/) ***，甚至参加一个机器学习*** [***比赛***](https://www.kaggle.com/) ***去寻找可以帮助你的专家。***

# 开始做出贡献

你现在处于良好的状态，可以开始为机器学习项目做出贡献。

请记住，您应该首先检查是否有与您的项目最相关的算法的现有实现，而不是从头开始编写所有内容。你检查过开源选项吗？几乎每一种已知的机器学习算法都有实现，你很可能会找到一种适合你的项目的算法。例如，前面提到的 scikit-learn 库提供了许多开箱即用的机器学习解决方案，TensorFlow 库包含了许多流行的深度学习算法的示例。

对于 OCR 项目，我的团队使用了 [deep-npr](https://github.com/matthewearl/deep-anpr) 项目作为起点。在通过最初的实验发展了一些直觉之后，我们转向了另一个名为 [crnn](https://github.com/bgshih/crnn) 的开源项目，它基于 CNN 和 rnn 的结合，并被证明对我们的问题是有效的。

有了选定的算法，你的大部分时间可能会花在优化所谓的[超参数](https://en.wikipedia.org/wiki/Hyperparameter_optimization)上，比如学习率、正则化、辍学等。你可能还会面临一些问题，比如决定是应该收集更多的训练数据，还是使用更深层次的神经网络。对于这些问题，机器学习专家可能会提供很多很有见地的建议。我还强烈推荐应用深度学习的演示文稿[螺母和螺栓](https://www.youtube.com/watch?v=F1ka6a13S9I)，其中包含适用于许多机器学习项目的非常实用的建议。

# 保持兴奋

我仍然记得在学习机器学习时我是多么兴奋，这是我一生中只发生过几次的经历。机器学习是一个非常强大的工具，每个软件工程师都应该并且能够掌握。无论你是否在学校学过数学，无论你是否有 Python 的经验，这些东西都可以在工作中开发，并得到外部教程、课程和专家建议的补充帮助。所以，各位开发人员——现在就行动起来，准备好被机器学习的力量所震惊，用你可以带到项目中的机器学习技能来惊艳你的团队。