# 欧特，你在哪里？

> 原文：<https://medium.com/square-corner-blog/oauth-wherefore-art-thou-b7034098a0fd?source=collection_archive---------3----------------------->

## 为什么我们必须使用 OAuth？

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

![](img/12a8ab10b7a61f26271e907410fed83d.png)

莎士比亚的戏剧是美丽和浪漫的精彩表现。他的戏剧也很复杂，悲剧，难以理解。和 OAuth 一起工作感觉很像莎士比亚。这听起来过于复杂，但你花的时间越多，你就越能看到潜在的美，并开始真正理解它。

让我们揭开 OAuth 的某些方面，这样您也可以看到使用它的好处和好处。如果您已经熟悉了 OAuth，那么请直接跳到 Square 来看看基本的 OAuth 设置。

在我们开始解释 OAuth 之前，有必要讨论一下何时应该实现它。许多 API 要求*利用* OAuth，但是有时你可能并不真的需要使用它。如果您只是为了自己的使用或自己的应用程序构建一个集成，那么使用您的个人访问令牌可能就可以了。如果您想要构建一个需要访问多个帐户或者需要细粒度访问的应用程序，那么您会想要构建一个 OAuth 集成。即使对于您自己的基础设施，如果您有一个 [ETL](https://en.wikipedia.org/wiki/Extract,_transform,_load) 服务，您可能希望专门为那个具有只读访问权限的服务创建一个访问令牌。

## 奥厄斯的幕后

为了解释 OAuth，我们将像在戏剧中表演一样进行演练。第一个动作是授权，第二个动作是重定向，第三个动作是获取。还有一个开场白——让您的授权者(在本例中是 Square)为注册您的应用程序做好准备，并获得在这个过程中使用的所有特殊凭证。

在第一个动作中，我们将用户发送到授权服务器请求一些权限——就像一个想要去实地考察的孩子。为了参加，他们会把实地考察需要的清单寄给父母，父母必须签字同意。对于 OAuth，您将一个用户发送给具有您希望拥有的范围(权限)的授权者。然后，用户可以看到所有被请求的内容，并确认他们愿意授予您的应用程序访问这些权限。

第二个动作是重定向，Square 向您的应用程序发回一个代码，该代码可用于获取访问令牌。如果你曾经使用 SMS 登录过什么东西，这应该看起来很熟悉。您需要向*和*提供您的密码和通过短信发送的代码才能登录。从 Square 发送回您的应用程序的代码是您的授权代码，您的应用程序在获取访问令牌的最后一步中使用它。

第三步是我们最终从 Square 接收访问令牌，这样我们就可以为用户处理 API 请求。这里，我们需要提供我们在重定向到回调 url *和*时收到的授权码，以及我们自己的应用程序秘密(在向 Square 注册时获得)，以便获得我们用户的访问令牌。然后，可以使用访问令牌代表我们的用户进行 API 调用。

## 好处

我们已经进行了一些类比，很明显，这个过程比简单地要求用户输入密码或让用户为您获取特殊的访问令牌要复杂得多。复杂性确保了流程的安全性，但也赋予了我们更多的灵活性。

如果我们简单地要求用户输入密码，而他们忘记了他们的 Square 密码，那么我们将不得不用这个新密码更新我们的系统，我们的集成可能会在此期间中断 ***(还有，永远不要要求别人的密码)*** 。我们还让最终用户能够优雅地撤销访问权限，而无需更改他们的密码。

此外，因为我们预先指定了范围，所以我们只需要提供对指定内容的访问。这意味着最终用户可以对授予什么做出明智的决定。提供密码时，您无法真正控制正在访问的内容。要么全有，要么全无。

## 实施

为了巩固我们对 OAuth 的理解，我们将实际演练 Square 的 OAuth 的一个实现。在[https://glitch.com/~square-oauth-example](https://glitch.com/~square-oauth-example)有一个全功能的例子。你可以看一下源代码，甚至重新混合它，让它与你自己的帐户一起工作；请务必在`.env`文件中添加您自己的变量。我们将讨论该过程的核心部分，但不会详细讨论诸如存储用户凭证或如何最好地处理会话持久性之类的事情。

该示例是使用 Express 和 SQLite 构建的。Express 允许我们将用户定向到正确的授权 URL 并处理回调，而 SQLite 允许我们存储令牌，并最终更新或撤销令牌。最后一步对于完整的实现非常重要，因为我们不希望用户每隔 30 天(访问令牌的有效期)就必须重新授权我们的应用程序。

在开始为我们的实现编写代码之前，我们需要注册一个 Square 开发者帐户。我们需要创建一个应用程序，获取我们的应用程序机密，并设置我们的重定向 URL。在用户登录并授予我们的应用程序访问其帐户的权限后，重定向 URL 将被发送到*。*

我们将开始创建我们的路由，用于处理将用户发送到授权 URL，在那里我们获得我们的权限。以下是我们的路线:

Our authorize route start on line 14.

对于那些不熟悉 Express 的人来说，`req`参数只是我们的请求对象，而`res`参数是我们的响应对象。我们还使用 Mozilla 的`client-sessions`中间件来创建加密的 cookies，可以通过`req.auth`访问。我们的方法从检查用户是否已经被认证开始，因此我们可以跳过整个授权过程。如果用户之前没有得到我们的授权，我们会初始化他们的`state`，并使用`req.auth`将其存储在他们的 cookie 中，以便我们稍后可以验证它以减轻 [CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) 。之后，我们只需构造用于重定向用户的授权 URL。`CLIENT_ID`是我们在[https://connect.squareup.com/apps](https://connect.squareup.com/apps)为我们的特定应用找到的应用 ID。请注意`scope`以及我们如何将该应用程序限制为只读取`MERCHANT_PROFILE`，以限制我们对用户简档的只读访问。如果我们需要更多的访问，我们可以通过向`scope` URL 参数添加额外的值来请求更多。

接下来，我们想展示如何构造用于处理回调的重定向 URL。这是我们的例子(我要预先警告，这部分看起来有点密集):

Our callback route starts on line 33.

我们立即开始检查查询字符串中的`state`是否与用户的加密 cookie 中存储的内容相匹配，因为我们不想处理未经授权的请求。然后，我们简单地将我们的有效负载发送到`[https://connect.squareup.com/oauth2/token](https://connect.squareup.com/oauth2/token)`，以便为我们的用户获取访问令牌。接下来的事情就是简单地在 Square 的 API 中查询一些用户信息，并在数据库中创建我们的用户(我们使用 [Sequelize](http://docs.sequelizejs.com/) 作为 ORM 来简化事情)。我还想指出，我们只在 cookie 中存储用户的`id`,而不存储其他个人信息，因为即使 cookie 是加密的，我们也不想在那里存储敏感信息。您需要像对待密码一样对待访问令牌，并且您不会将密码存储在不安全的地方，所以对访问令牌也要这样做。在这个示例应用程序中，我们将在存储令牌之前对其进行额外的加密，并在需要使用时对其进行解密。

最后，我们将讨论更新和撤销。虽然这些步骤是可选的，但是它们对于创建更好的用户体验非常有用。Square 允许访问令牌在 30 天内保持有效，在此之后，您需要更新它们。您有 15 天的时间来续订访问令牌。

Our renew route begins on line 80.

更新步骤相当简单:我们只需将访问令牌发送到`[https://connect.squareup.com/oauth2/clients/${CLIENT_ID}/access-token/renew](https://connect.squareup.com/oauth2/clients/${CLIENT_ID}/access-token/renew)`，并在授权头中使用我们的应用程序秘密。

您可以在下面的示例中找到从第 105 行开始撤销令牌的详细信息:

Our revoke route begins on line 105.

撤销令牌甚至更容易，因为我们只需将我们的客户端 id 和访问令牌发送给`[https://connect.squareup.com/oauth2/revoke](https://connect.squareup.com/oauth2/revoke)`。

我们已经介绍了使用 Square 创建 OAuth 集成的过程中的每个步骤，以展示每个方法是如何工作的，但是有很多库可以为您简化这个过程( [PassportJS](http://www.passportjs.org/) 、 [Grant](https://github.com/simov/grant) 等等)。如果您以前从未实现过 OAuth，那么为了更好地理解过程的每一步，看到这样的完全集成是很有用的(这总是有助于以后的调试)。

我强烈建议你注册一个 [Square 开发者账户](https://squareup.com/developers)，尝试一下这种整合。您只需访问[https://square-oauth-example.glitch.me/](https://square-oauth-example.glitch.me/)并在那里授权您的 Square 帐户。请随意重新混合 Glitch 上的示例，尝试让您自己的*OAuth 集成工作起来。您也可以查看我们的 OAuth [指南](https://docs.connect.squareup.com/basics/oauth/setup)和[概述](https://docs.connect.squareup.com/basics/oauth/overview)以获得更多关于设置的说明。*

*想要更多？* [*注册*](https://www.workwithsquare.com/developer-newsletter.html?channel=Online%20Social&sqmethod=Blog) *获取我们每月的开发者简讯。*