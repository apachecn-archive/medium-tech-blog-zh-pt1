# 微服务安全——如何保护您的微服务基础设施？

> 原文：<https://medium.com/edureka/microservices-security-b01b8f2a9215?source=collection_archive---------0----------------------->

![](img/464968ecbefeb19efa7f6462627a6fce.png)

Microservices Security — Edureka

在当今市场中，各行业都在使用各种软件架构和应用程序，几乎不可能感觉到您的数据是完全安全的。因此，在使用微服务架构构建应用程序时，安全问题变得更加重要，因为各个服务相互之间以及与客户端进行通信。因此，在这篇关于微服务安全性的文章中，我将按以下顺序讨论保护微服务的各种方法。

*   什么是微服务？
*   微服务面临的问题
*   保护微服务的最佳实践

# 什么是微服务？

微服务，又名 ***微服务架构*** ，是一种架构风格，将应用程序构建为小型自治服务的集合，围绕**业务领域建模。**因此，您可以将微服务理解为围绕单一业务逻辑相互通信的小型个体服务。

![](img/1b7e6e0a48fadb03e96f1438dd92b818.png)

现在，通常当公司从单一架构转向微服务时，他们会看到许多好处，如可伸缩性、灵活性和短开发周期。但是，与此同时，这种架构也引入了一些复杂的问题。

因此，在这篇关于微服务安全性的文章中，让我们了解一下微服务架构面临的问题。

# 微服务面临的问题

微服务面临的问题如下:

## 问题 1:

考虑一个场景，其中用户需要登录来访问资源。现在，在微服务架构中，用户登录详细信息必须以这样的方式保存，即用户每次尝试访问资源时都不会被要求进行验证。现在，这产生了一个问题，因为用户信息可能不安全，也可能被第三方访问。

## 问题二:

当客户端发送请求时，需要验证客户端的详细信息，还需要检查授予客户端的权限。因此，当您使用微服务时，可能会发生这样的情况:对于每一项服务，您都必须对客户端进行身份验证和授权。现在，为了做到这一点，开发人员可能会对每个服务使用相同的代码。但是，你不觉得依赖一个特定的代码降低了微服务的灵活性吗？嗯，确实如此。因此，这是这种架构经常面临的主要问题之一。

## 问题三:

下一个非常突出的问题是每个微服务的安全性。在这个架构中，除了第三方应用程序之外，所有的微服务同时相互通信。因此，当客户端从第三方应用程序登录时，您必须确保客户端无法访问微服务的数据，否则他/她可能会利用这些数据。

好吧，上面提到的问题并不是微服务架构中发现的唯一问题。我认为，根据您的应用程序和架构，您可能会面临许多其他与安全性相关的问题。有鉴于此，让我们继续这篇关于微服务安全性的文章，并了解减少挑战的最佳方法。

# 微服务安全的最佳实践

提高微服务安全性的最佳实践如下:

## 纵深防御机制

众所周知，微服务在粒度级别上采用任何机制，您可以应用深度防御机制来使服务更加安全。通俗地说，深度防御机制基本上是一种技术，通过这种技术，您可以应用多层安全对策来保护敏感服务。因此，作为一名开发人员，您只需识别具有最敏感信息的服务，然后应用许多安全层来保护它们。通过这种方式，您可以确保任何潜在的攻击者都无法一蹴而就地破解安全性，而必须继续尝试破解所有层的防御机制。

此外，由于在微服务架构中，您可以在不同的服务上实现不同的安全层，成功利用特定服务的攻击者可能无法破解其他服务的防御机制。

## 令牌和 API 网关

通常，当您打开一个应用程序时，您会看到一个对话框，上面写着“接受 cookies 的许可协议和许可”。这条消息意味着什么？一旦您接受了它，您的用户凭证将被存储，一个会话将被创建。现在，下一次访问同一页面时，页面将从缓存中加载，而不是从服务器本身加载。在这个概念出现之前，会话集中存储在服务器端。但是，这是横向扩展应用程序的最大障碍之一。

## 代币

所以，解决这个问题的办法是使用令牌，来记录用户凭证。这些令牌用于轻松识别用户，并以 cookies 的形式存储。现在，每次客户端请求网页时，请求都会被转发到服务器，然后服务器会确定用户是否有权访问所请求的资源。

现在，主要问题是存储用户信息的令牌。因此，令牌的数据需要加密，以避免任何第三方资源的利用。Jason Web Format(通常称为 JWT)是一种开放标准，它定义了令牌格式，为各种语言提供了库，并且还对这些令牌进行了加密。

## API 网关

API 网关作为额外的元素通过令牌认证来保护服务。API 网关充当所有客户端请求的入口点，并有效地对客户端隐藏微服务。因此，客户端无法直接访问微服务，因此，没有客户端可以利用任何服务。

# 分布式跟踪和会话管理

## 分布式跟踪

使用微服务时，您必须持续监控所有这些服务。但是，当您必须同时监控大量的服务时，这就成了一个问题。为了避免这样的挑战，您可以使用一种称为分布式跟踪的方法。分布式跟踪是一种查明故障并确定故障原因的方法。不仅如此，你还可以识别失败发生的地方。所以，追踪哪个微服务面临安全问题是非常容易的。

## 会话管理

会话管理是保护微服务时必须考虑的一个重要参数。现在，每当用户进入应用程序时，就会创建一个会话。因此，您可以通过以下方式处理会话数据:

1.  您可以将单个用户的会话数据存储在特定的服务器中。但是，这种系统完全依赖于服务之间的负载平衡，并且只满足水平扩展。
2.  完整的会话数据可以存储在单个实例中。然后可以通过网络同步数据。唯一的问题是，在这种方法中，网络资源会耗尽。
3.  您可以确保可以从共享会话存储中获取用户数据，从而确保所有服务都可以读取相同的会话数据。但是，因为数据是从共享存储中检索的，所以您需要确保有某种安全机制，以安全的方式访问数据。

## 首次会话和相互 SSL

第一届的想法很简单。用户只需登录一次应用程序，就可以访问应用程序中的所有服务。但是，每个用户必须首先与认证服务通信。嗯，这肯定会导致所有服务之间的大量流量，并且对于开发人员来说，在这种情况下找出故障可能很麻烦。

说到相互 SSL，应用程序经常面临来自用户、第三方以及微服务之间相互通信的流量。但是，由于这些服务是由第三方访问的，因此总是存在被攻击的风险。现在，这种场景的解决方案是微服务之间的相互 SSL 或相互认证。这样，服务之间传输的数据将被加密。这种方法的唯一问题是，当微服务的数量增加时，由于每个服务都有自己的 TLS 证书，开发人员很难更新证书。

# 第三方应用程序访问

我们所有人都访问第三方应用程序。第三方应用程序使用用户在应用程序中生成的 API 令牌来访问所需的资源。因此，第三方应用程序可以访问特定用户的数据，而不是其他用户的凭据。嗯，这是针对单个用户的。但是，如果应用程序需要访问多个用户的数据，该怎么办呢？你认为如何满足这样的要求？

## OAuth 的用法

解决方案是使用 OAuth。当您使用 OAuth 时，应用程序会提示用户授权第三方应用程序，使用所需的信息，并为其生成一个令牌。通常，授权码用于请求令牌，以确保用户的回调 URL 不被窃取。

因此，当提到访问令牌时，客户端与授权服务器通信，并且该服务器授权客户端防止其他人伪造客户端的身份。因此，当您将微服务与 OAuth 一起使用时，这些服务在 OAuth 架构中充当客户端，以简化安全问题。

好吧，伙计们，我不会说这些是唯一的方法，通过它们你可以确保你的服务。基于应用程序的架构，您可以通过多种方式保护微服务。因此，如果你希望构建一个基于微服务的应用，那么请记住，服务的安全性是你需要小心的一个重要因素。说到这里，我们将结束这篇关于微服务安全性的文章。我希望你发现这篇文章信息丰富。

如果你想查看更多关于人工智能、DevOps、道德黑客等市场最热门技术的文章，你可以参考 Edureka 的官方网站。

请留意本系列中的其他文章，它们将解释微服务的各个方面。

> *1。* [*什么是微服务？*](/edureka/what-is-microservices-86144b17b836)
> 
> *2。* [](https://www.edureka.co/blog/object-oriented-programming/?utm_source=medium&utm_medium=content-link&utm_campaign=java-exception-handling)[*微服务架构*](/edureka/microservice-architecture-5e7f056b90f1)
> 
> *3。* [*微服务 vs SOA*](/edureka/microservices-vs-soa-4d71c5590fc6)
> 
> *4。* [*微服务教程*](/edureka/microservices-tutorial-with-example-a230413dfa13)
> 
> *5。* [*构建微服务应用使用 Spring Boot*](/edureka/microservices-with-spring-boot-ffab2ce8ac34)
> 
> *6。* [*微服务安全*](/edureka/microservices-security-b01b8f2a9215)

*原载于 2019 年 8 月 9 日*[*https://www.edureka.co*](https://www.edureka.co/blog/microservices-security)*。*