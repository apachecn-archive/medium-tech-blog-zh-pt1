# 码头工人公证人:非常 TUF，但细节决定成败！

> 原文：<https://medium.com/walmartglobaltech/docker-notary-very-tuf-but-devil-is-in-the-detail-5e643ea0aa16?source=collection_archive---------2----------------------->

![](img/d0b75fe4175fd9ab9702955f136fc0dd.png)

**Docker 内容信任**

软件分发系统需要一种机制来证明它们正在分发的软件的可信度，比如提供签名来证明图像及其发布者，证明其完整性、新鲜度等。传统上，软件映像分发系统使用 GPG 密钥对映像进行签名。对图像进行签名确实证明了发布者的身份和所发布图像的真实性。然而，只有签名并不能提供几种安全机制，比如在签名密钥泄露后仍然存在的能力。

Docker 通过提供一个名为 Docker Content Trust 的底层框架来解决这个问题，该框架在运行容器之前验证图像。不仅如此，它还会不断地轮询 Docker 注册表中的映像更新，如果有，就会在启动容器之前获取更新。执行和管理这种信任背后的引擎是 Docker 公证人，这是一个基于[更新框架](https://theupdateframework.github.io/) (TUF)实现的 Docker 服务。

TUF 是一个用于安全软件分发的 T4 规范。它使用由非对称密钥表示的角色层次结构和使用这些非对称密钥签名的元数据来建立信任。根密钥、快照密钥、时间戳密钥和目标密钥的密钥层次结构一起提供了几种安全保证，如新鲜度保证、可生存密钥折衷等。根密钥是签署根元数据的所有信任的根。快照元数据是映像的给定快照的所有文件的散列集合。

发布的每个图像都使用服务器端和发布者端密钥的组合来签名，以确保攻击者仅破坏服务器密钥不足以篡改图像和发布恶意图像。公证人还建议离线存储根密钥，以防止泄露。

**不过，有个小问题！**

在上述元数据中，时间戳元数据旨在确保内容的新鲜度，并且具有非常短的到期时间。它是使用时间戳密钥签名的，为了方便起见，时间戳密钥总是在线的，因为客户端经常通过检查时间戳来获取时间戳以进行新鲜度检查。如果没有任何变化，客户端的映像可以按原样使用。

在公证人的实现中，服务器端私有密钥作为使用 AES 对称密钥加密的密文存储在密钥库数据库中。但是，这个 AES 对称密钥以明文形式存储在公证人签名者服务的环境变量中。环境变量被证明是一个非常不安全的位置，因为它们没有任何访问控制。这可能是密钥链中最薄弱的一环，使得整个 Docker 注册表和这个密钥一样安全。

**威胁**

这里讨论的威胁是一个**冻结攻击**，它是弱存储对称密钥泄露的结果:

攻击者可以访问受弱保护的 AES 对称密钥。

攻击者从密钥库数据库获取时间戳密钥的密文，并解开 AES 包装的私钥。

使用很长的过期时间对时间戳元数据进行重新签名。

客户端轮询时间戳元数据到期，并确认它没有到期并且仍然有效。因此，没有必要重新获取图像。

在时间戳过期之前，客户端永远不会得到更新，因此它只能使用旧的、有潜在漏洞的映像版本。

**解决方案**

对于像公证人这样必须随时可用并且应该在没有任何人工干预的情况下自动出现的服务来说，安全地存储这个最终秘密并不是一个很容易解决的问题，因为总会有一个最终秘密需要维护。

这里提出的解决方案是确保对称 AES 密钥(整个链中的最终秘密)使用安全存储机制(如 HSM/金库)存储，以防止泄露，但在运行时仍可按需获得。

但是，支持所有/任何可能的外部安全存储方式超出了 Docker 公证人的范围。此外，如果公证代码库了解外部机制，则必须重新构建并发布它，以支持任何新的安全存储机制。

公证人将需要提供一个插件接口，用于在运行时使用符合该插件接口的外部插件进行外部金库/HSM 集成。这也使得公证未来证明任何未来的安全存储方法。

下图描述了建议的解决方案:

![](img/c870c8fe32fa165091209eefd3165d25.png)

在上图中，提议的改变仅仅是引入一个插件接口。所有插件开发都不在 Docker 公证人的范围之内。

**总结**

码头公证人在确保秘密安全方面走了很长的路，但错过了最后一个秘密。这种解决方案有助于将秘密存储在硬件中，从而解决了最后一个秘密难题。这是我与 Docker 公证人一起为外部安全存储集成创建插件接口的一个问题:[https://github.com/docker/notary/issues/1091](https://github.com/docker/notary/issues/1091)

这是我在 Docker 公证人的 github 库上的拉请求，请求引入一个接口来容纳插件接口，以便在运行时动态加载外部存储插件:[https://github.com/docker/notary/pull/1177](https://github.com/docker/notary/pull/1177)