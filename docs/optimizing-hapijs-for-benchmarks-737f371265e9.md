# 为基准优化 HapiJS

> 原文：<https://medium.com/walmartglobaltech/optimizing-hapijs-for-benchmarks-737f371265e9?source=collection_archive---------0----------------------->

![](img/c6c2c39a130c93bc12b5726be2d33a88.png)

在过去一年多的时间里，我们的团队在 [@WalmartLabs](https://medium.com/u/c884135151a4?source=post_page-----737f371265e9--------------------------------) 成功开发并标准化了一个使用 NodeJS 开发和部署企业应用的平台[电极](http://www.electrode.io/)。

在做出的许多架构决策中，其中之一是使用哈比神作为框架来支持我们的 web 服务器。因此，我们不断收到有关性能的问题，许多基准测试表明 express 的性能是哈比神的 X 倍。

就像任何其他架构决策一样，性能只是拼图的一部分。哈比神的创造者 [Eran Hammer](https://medium.com/u/b4e3921706ee?source=post_page-----737f371265e9--------------------------------) 为此写了一篇非常好的博客[。尽管如此，除了性能之外，我没有解释哈比神带来的其他重要特性，如安全检查和验证，而是决定深入研究细节，看看哈比神到底做了什么来牺牲性能，看看是否有任何优化的机会。](https://hueniverse.com/performance-at-rest-75bb8fff143)

我们选择哈比神的一些原因是:

*   定义明确的约定和已建立的应用程序结构，并进行验证
*   它有一个独立于请求生命周期的插件系统
*   它有安全检查和配置验证
*   它带有请求生命周期中的默认特性，如缓存和授权
*   它有一个内置的事件记录系统
*   鉴于它已经在这里开发了一段时间，哈比神在 [@WalmartLabs](https://medium.com/u/c884135151a4?source=post_page-----737f371265e9--------------------------------) 有一些普遍的曝光

基准性能并不是哈比神的优先考虑事项之一，正如其创建者所解释的那样，各种基准表明:

*   [*techempower.com*](https://www.techempower.com/benchmarks/)
*   [raygun.com](https://raygun.com/blog/node-js-performance-comparison/?utm_source=RG_BLOG&utm_medium=article_link&utm_content=node_performance_2016)

显然，任何框架和节点的原始样本之间的区别在于接收请求和发回响应之间插入了多少代码。例如，至少，对于任何有用的框架，首先需要的是通过 URL 匹配进行路由。

我将跟踪哈比神的请求生命周期代码路径，看看它在响应发送之前做了什么，然后对它运行 V8 概要分析，看看热点在哪里。

首先，在我们运行 CentOS 7 和 Node 6.10.0 的商用服务器上运行 express 和哈比神的简单基准测试时的一些快速基数:

*   运行示例的单个实例
*   使用`ab -c 64 -n 10000 [http://localhost:8000](http://localhost:8000`)`进行查询

我得到的大概数字是快车 *1550 请求/秒*和哈比神 *550 请求/秒*。哈比神的这种开销是不变的。它显示了 3 倍的差异，因为完全没有业务逻辑。如果您的逻辑占用了很大一部分响应时间，那么哈比神的开销只是很小的一部分，而 express 在整体响应时间上只占很小的一部分。

接下来，当收到请求时，我通过哈比神的代码进行跟踪。以下是哈比神在请求生命周期中经历的有趣点的概述:

*   第一个节点的`http`服务器发出`request`事件，该事件由`*_dispatch*` *中的`Connection`处理。*
*   哈比神创建了自己的`Request`对象，包装了`http`请求。
*   哈比神有过载保护功能，所以它会进行负载检查。
*   哈比神执行请求生命周期，用节点的域保护它。
*   请求中的第一件事是检查 URL 路由。
*   设置响应超时。
*   执行生命周期步骤。

生命周期的步骤相当简单:

*   首先检查`*hapi/lib/route.js*`中的 cookies
*   在`*hapi/lib/auth.js*`中检查授权
*   最后调用路由处理器
*   路由处理器进行一些初始化以测量定时基准

因为示例非常简单，所以没有 cookies 或 auth，所以跳过了它们。

我注意到两件事:

*   哈比神默认使用节点的域来保护请求生命周期的执行
*   默认情况下，哈比神有一个调试设置，因此它订阅服务器日志事件

在创建服务器时，可以使用选项关闭这两个选项，但是，这样做不会对性能产生任何有意义的改变。

在请求生命周期代码路径中没有其他突出的东西，所以我求助于 V8 的分析器。

*   使用`*NODE_ENV=production node --prof*`运行样本
*   用`ab`查询服务器

来自分析器的结果显示，最成功的是大量的`Joi`验证和`Podium`事件处理。

在`*joi/lib/any.js:442*`设置断点我追踪到这些调用源自`Podium`，并且指向`*podium/lib/index.js*`中的两行代码，这些代码调用`*Joi.attempt*`来验证事件对象。

为了验证这一发现，我注释掉了这两行，并再次运行计时检查。结果是哈比神现在可以每秒处理超过 1000 个请求。基准性能提高了一倍，现在只比 express 低一点点。

再次运行分析器，现在只有`Podium`事件处理显示为最热门。鉴于哈比神的内部系统严重依赖它，这并不奇怪。

我决定停在这里，因为`Podium`显然是一个特性丰富的事件发射器，不受性能要求的限制，与 express 相比，它很可能是剩余额外开销的主要原因。进一步挖掘，看看开销分解成什么，肯定会很有趣。

优化的机会可能是避免重新验证`Podium`中的所有事件。例如，在`*hapi/lib/request.js*`中，当一个请求被创建时，它立即用一个包含三个静态事件的数组调用`Podium`，对于每个传入的请求，这些静态事件不需要用`Joi`进行验证。

**结论**:哈比神是一个专注于为开发可靠的业务应用程序提供基础设施的框架。它在诸如严格检查和验证等功能的“基准性能”上进行权衡，这些功能在数百名开发人员开发应用程序时非常有用。虽然大多数都是应用启动期间的配置验证，不会对热代码路径产生影响，但哈比神的`Podium`自定义事件发射器在运行时对事件的验证增加了许多冗余开销，甚至对静态事件对象也是如此。在某种程度上，这几乎就像 V8 引擎每次执行时都重新验证相同的 JS 代码。即使在“基准性能”方面做得很好没有太大的意义，但是如果有一种方法可以重用这些验证或者在生产中直接关闭它们，这将有助于从哈比神的请求生命周期中减少一些 ms。

感谢[戴夫·史蒂文斯](https://medium.com/u/9f42c93e61de?source=post_page-----737f371265e9--------------------------------)审阅这篇文章。