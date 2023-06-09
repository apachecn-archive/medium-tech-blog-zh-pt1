# 为 Airbnb 住宿排名改进深度学习

> 原文：<https://medium.com/airbnb-engineering/improving-deep-learning-for-ranking-stays-at-airbnb-959097638bde?source=collection_archive---------1----------------------->

由[马来豪达](/@malay.haldar) & [驼鹿阿普杜尔](/@mooseabdool)

![](img/191e47d78ca8974028907072f05238f5.png)

搜索排名是 Airbnb 的核心。来自搜索日志*的数据表明，超过 90%的客人使用这一功能来预订住宿。在排名中，我们希望搜索结果(称为列表)按照客人的偏好进行排序，为此我们训练了一个深度神经网络(DNN)。

DNN 推断客人偏好的一般机制是通过查看过去的搜索结果以及与所显示的每个列表相关联的结果。例如，已预订的列表被认为优先于未预订的列表。因此，根据预订量的变化对 DNN 的变化进行分级。

之前，我们已经关注了如何有效地将 DNNs 应用到学习客人偏好的过程中。但是这个学习过程有了一个飞跃——*它假设未来客人的喜好可以从过去的观察中学习*。

在本文中，我们将超越基本的 DNN 建筑设置，更详细地研究这一假设。在这个过程中，我们描述了简单的学习排名框架的不足之处，以及我们如何解决这些挑战。我们的解决方案是搜索排名的 *A、B、C、D* :

*   **建筑:**我们能否以一种能让我们更好地代表顾客偏好的方式来构建 DNN？
*   **偏倚:**能否剔除过去数据中存在的一些系统性偏倚？
*   **冷启动**:鉴于新上市公司缺乏历史数据，我们能否纠正它们面临的不利条件？
*   **搜索结果的多样性:**我们能否避免过去数据中的多数偏好压倒未来的结果？

# 体系结构

更好的建筑的动机来自于这样一个事实，即 DNN 推断的游客偏好似乎与实际观察到的偏好脱节。特别是，客人预订倾向于经济价格的列表，预订列表的中间价格低于显示的搜索结果的中间价格。这表明我们可以通过展示更多价格更低的房源来接近真正的客人偏好，这种直觉我们称之为*越便宜越好*。然而，对 DNN 排名结果明确应用基于价格的降级导致预订量下降。

对此，我们抛弃了“越便宜越好”的直觉，意识到我们真正需要的是一个架构来预测旅行的“T2”理想列表“T3”。该架构有两座塔楼，如下图 1 所示。一个由查询和用户特征提供信息的塔预测了旅行的理想列表。第二个塔将原始列表特征转换成向量。在训练期间，训练塔，使得预订的列表更接近理想列表，而未预订的列表被推离理想列表。当在受控的 A/B 实验中进行在线测试时，该架构成功地将预订量提高了+0.6%。

![](img/e48226ea4e4c7667ad98932284ff9829.png)

Figure 1\. Query and listing tower architecture

# 偏见

从过去的预订中推断客人偏好的一个挑战是预订决策不仅仅是客人偏好的函数。它们还受到搜索结果中列表显示位置的影响。随着我们在结果列表中往下走，用户的注意力单调下降，因此我们可以推断，排名较高的列表有更好的机会被预订，这完全是因为它们的位置。这就形成了一个反馈循环，以前模型中排名较高的列表在未来会继续保持较高的位置，即使它们可能与客人的偏好不一致。下面的图 2 显示了一个列表的点击量是如何随着它在排名中的位置而衰减的，与列表的质量无关。衰减按设备平台显示。

![](img/2edbd3174b1cc9e6de9e1e7d11d0ad00.png)

Figure 2\. Click through rates by position in search results

为了解决这种偏见，我们在 DNN 中添加了位置作为一项功能。为了避免过度依赖位置特征，我们将它与辍学率一起引入。在这种情况下，我们在训练过程中有 15%的时间将位置特性设置为 0。这个附加信息让 DNN 了解列表的位置和质量对用户预订决定的影响。在为未来用户排列列表时，我们将输入位置设置为 0，有效地平衡了所有列表的竞争环境。纠正位置偏差后，在线 A/B 测试中的预订量增加了+0.7%。

# 冷启动

我们不能依赖过去数据的一个明显情况是以前的数据不存在。这一点在 Airbnb 平台的新房源上表现得最为明显。通过离线分析，我们观察到，一旦收集到足够的数据，新上市公司的排名还有很大的改进空间，特别是相对于它们的“稳态”行为。

为了解决这个冷启动问题，我们开发了一种更准确的方法来估计新上市公司的参与度数据，而不是简单地对所有新上市公司使用全局默认值。这种方法考虑由地理位置和容量测量的*相似列表、*,并从那些列表中聚集数据，以产生对新列表将如何表现的更准确估计。在一项受控的在线 A/B 测试中，这些对新房源参与度的更准确预测导致新房源预订量增加了+14%,总预订量增加了+0.4%。

# 搜索结果的多样性

深度学习使我们能够创建一个强大的搜索排名模型，该模型可以根据任何个人列表的过去表现来预测其相关性。然而，缺少的一个角度是向用户展示的结果的更全面的视图。通过一次只考虑一个列表，我们无法优化整个结果集的重要属性，比如多样性。事实上，我们观察到许多热门搜索结果在关键属性上看起来很相似，比如价格和位置，这表明缺乏多样性。一般来说，多样化的结果可以通过展示广泛的可用选择而不是多余的项目来提供更好的用户体验。

我们解决多样性的解决方案涉及开发一种新的深度学习架构，它由递归神经网络(RNNs)组成，使用*整个结果序列生成查询上下文的嵌入。*这个*查询上下文嵌入*然后用于根据关于整个结果集的新信息对输入列表重新排序。例如，该模型现在可以学习本地模式，并在某个列表是该搜索请求的热门区域中唯一可用的列表之一时对其进行升级。生成这个*查询上下文嵌入*的架构如下面的图 3 所示。

![](img/6a4841b7f82effc53c95c76aaf12cbfa.png)

Figure 3\. RNN for search result context

总的来说，我们发现这增加了我们搜索结果的多样性，同时在在线 A/B 测试中全球预订量增加了+0.4%。

上述技术使我们能够超越基本的深度学习设置，并继续为 Airbnb 上的所有搜索服务。也就是说，这篇文章仅仅触及了我们的 DNN 如何工作的几个考虑因素。最终，我们在确定搜索排名时会考虑 200 多个信号。随着我们寻求进一步的改进，对顾客偏好的深入了解仍然是我们的指路明灯。

# 进一步阅读

我们在 KDD 会议上发表的论文具有更深的技术深度:

*   [**改善 Airbnb 搜索的深度学习**](https://arxiv.org/pdf/2002.05515.pdf) 深入神经网络架构的细节，解决位置偏差和冷启动。KDD 2020
*   [**在 Airbnb 搜索中管理多样性**](https://arxiv.org/pdf/2004.02621.pdf) 致力于我们用来提高搜索结果多样性的技术。KDD 2020
*   [**将深度学习应用于 Airbnb 搜索**](https://arxiv.org/pdf/1810.09591.pdf) 描述了如何有效地将 DNNs 应用于搜索排名。KDD 2019

我们总是欢迎读者的想法。对这项工作感兴趣的人，请查看搜索团队的 [*空缺职位*](http://bit.ly/AirbnbSearchRelevanceHiring) 。

*数据收集于 2020 年 8 月的前两周。