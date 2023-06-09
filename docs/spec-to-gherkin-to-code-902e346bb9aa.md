# 规范到小黄瓜到代码

> 原文：<https://medium.com/capital-one-tech/spec-to-gherkin-to-code-902e346bb9aa?source=collection_archive---------0----------------------->

## 基于 Swagger 和 OAS 的接力赛

![](img/780056b3c3a7015947489cba5d2bb903.png)

短语*“Spec to Gherkin to Code”*听起来几乎就像体育广播员在喊棒球双杀，从游击手到二垒手再到一垒手。良好同步的团队合作的理想是我们如何从写在 [OpenAPI 规范](https://github.com/OAI/OpenAPI-Specification) (OAS，以前的 Swagger)中的 API 描述转移到基于[小黄瓜](https://cucumber.io/docs/reference)的验收测试，再到实际的工作代码。我感兴趣的是移交的细节，以及在过程的每个阶段信息是如何保存的。

这似乎与通过平衡优势和劣势来建立团队的本质相类似。一项运动的规则导致需要建立专门的技能。组建一支棒球队需要九个人；构建一个有效的应用程序需要多种工具和语言。当所有球员都需要的时候，把一个球员提升为“最有价值”是错误的。

记住目的很重要:*工作代码。*但是单独运行应用程序代码*——是不够的。除了代码，我们还需要一些基础来证明代码真的有效。如果你是一个棒球迷，并且支持一场出色的防守，你会很高兴看到裁判判跑垒员出局。有一个裁判为刚刚发生的事情提供正式的见证是很重要的。在软件领域，裁判的判断被自动化测试所取代。当我们查看各种测试工具时，我想起了三位棒球裁判，他们努力做到公平:*

***裁判# 1:**“*有球就有好球，我就叫它们本来的样子。”**

*裁判#2: *“我只是一个普通人，我所能做的最好的事情就是按照我所看到的来称呼他们。”**

*裁判#3: *“你们两个都错了:在你叫他们之前，他们什么都不是。”**

*裁判#3 的观点与自动化测试和验收测试驱动开发(ATDD)相似。我们的代码没有工作，直到我们有自动化测试来确认我们对正确行为的主观印象。没有测试结果，就没有可用的代码。*

*我认为这有助于澄清 OpenAPI 规范和小黄瓜的作用。最终的工作代码只是解决方案的一部分。测试用例至少和代码一样重要，在某些情况下，测试用例可能是唯一更重要的工件，因为它们可能是正确行为的唯一有意义的定义。*

*然而，试图管理 Gherkin、OpenAPI 和代码，并让它们成功地合作可能会有问题。我们的玩家有不同的语言，但他们应该都在玩一个游戏。我们可以创建一个小的 Python 工具来确保语言匹配。*

## *RESTful API 和 OpenAPI 规范*

*我将从 OpenAPI 规范开始，因为我偏向于编写 RESTful API，而编写到 OpenAPI 规范中的 API 描述提供了大量的细节，这些细节很容易转换成小黄瓜*。* ***注意——在这篇文章中，我将重点介绍 OAS 版本 2，而不是 OAS 版本 3。****

*虽然 OpenAPI 规范在技术上是一个简单的 JavaScript 对象符号(JSON)文档，但是读取和编写 JSON 可能很困难。因此，很多人更喜欢使用 YAML(另一种标记语言)符号来编写 OpenAPI 规范的 API 描述。如果你去[https://swagger.io/](https://swagger.io/)打开在线编辑器，它可以用 YAML 符号工作。*

*OpenAPI 规范受到底层 HTTP 规则的约束。规范中没有详细说明这些规则。形式上，它们是在 RFC 中定义的，RFC 定义了规范部分的语义。虽然规范中没有说明行为细节，但是在为验收测试创建小黄瓜时需要说明。*

*写入 OpenAPI 规范的 API 描述定义了服务器处理的 URL 路径。每条路径中都有操作。这些是应用于由路径定义的资源的动词。HTTP 对可用的动词施加了一些限制，这要求在适当地选择操作时需要一些技巧。Gherkin 没有 OpenAPI 规范那样狭隘的关注点。*

*这里有一个符合我们介绍中棒球主题的具体例子:*

```
*swagger: ‘2.0’
info:
 title: A Quick Demo
 version: ‘1.0’paths:

 /umpires:
   get:
    summary: The umpires
    responses:
     200:
       description: The umpire list
       schema:
         type: object
         properties:
            status:
              type: string
              example: “OK”
            data:
              type: array
              items:
                type: string
              example: [“Tinker”, “Evers”, “Chance”]
     403:
       description: Error*
```

*我们已经定义了一个 RESTful API，其中有一个资源/umpires，并且只有一个针对该资源的操作 GET。名称/umpires 是复数，以强调它是资源的分类，而不是资源的单个实例。*

*schema 部分提供了响应文档的定义。模式定义的细节可能很难可视化。一个例子会有所帮助:下面是示例文档*

```
*{
    “status”: “OK”,
    “data”: [
       “Tinker”,
       “Evers”,
       “Chance”
    ]
 }*
```

*本文档是根据 OpenAPI 规范中提供的示例构建的。示例值是从规范到代码的核心。*

## *验收测试和小黄瓜*

*一个非常流行的验收测试工具是黄瓜，这个工具使用小黄瓜语言规范和基于 Ruby 的步骤定义。我更熟悉[行为](http://pythonhosted.org/behave/)，因为它是 Cucumber 的一个很好的 Python 实现。阅读更多关于[用小黄瓜和黄瓜](/@mvwi/story-writing-with-gherkin-and-cucumber-1878124c284c)写更好的用户故事。*

*与 OpenAPI 规范一样，Gherkin 本质上倾向于声明性的。规范本身不是工作代码，它们是对代码预期行为的描述。Gherkin 语句是被动编写的，允许用命令式代码灵活实现。*

*下面是一个小黄瓜场景规范的例子:*

```
*Scenario: Get the list of umpires
 Given a test microservice
 And umpires [‘Tinker’, ‘Evers’, ‘Chance’]
 When request is “/umpires”
 Then response body includes [‘Tinker’, ‘Evers’, ‘Chance’]*
```

*这是一个正式的结构，包含三个步骤定义:*

*   *给定—指定上下文。*
*   *When —这将指定一个 RESTful API 请求。*
*   *那么——这描述了预期的响应。*

*Gherkin 结构是一种在任何粒度级别指定行为的优雅方式。可应用于函数、类定义、模块、包、微服务、框架、数据库等。一切有行为的地方。*

*如果小黄瓜特征文件不是代码，它们是如何变成测试的？*

*我们需要编写步骤定义，在用问题领域术语编写的特性和在解决方案领域工作的测试用例之间架起一座桥梁。使用 Behave 时，我们可能会有一个 steps/prejure . py 文件，其中包含以下几种定义:*

```
*from behave import *

 @given(‘Given a test microservice’)
 def create_client(context):
 # Some test client setup goes here.
 pass

 @given(“umpires [‘Tinker’, ‘Evers’, ‘Chance’]”)
 def set_database(context):
 # Some database setup goes here.
 pass

 @when(‘request is “/umpires”’)
 def make_get_umpires_request(context):
 # The RESTful API request goes here.
 pass

 @then(“response body includes [‘Tinker’, ‘Evers’, ‘Chance’]”)
 def assert_response(context):
 # Check the response goes here.
 pass*
```

*这里显示的代码有基于小黄瓜场景的占位符。pass 语句必须替换为工作测试代码。我加入了一些注释来帮助说明需要编写什么样的代码。*

*例如，如果服务器是在[http://flask.pocoo.org/,](http://flask.pocoo.org/,)中实现的，那么 create_client()函数将需要构建应用程序并为应用程序创建一个 Flask 测试客户端。*

*运行测试时，behavior 将使用特征文件和步骤规范来执行测试并确认结果。输出将包括一个日志以及一个 JSON 文档，其中包含官方结果，以显示软件的行为是可以接受的。*

> **运行 Behave 或 pytest-bdd 创建的 JSON 输出是正式裁判发出的“安全”或“出界”信号的体现。这就是导致粉丝爆发出喜悦或悲伤的原因。**

*小黄瓜经常被吹捧为让非技术人员投入软件正确行为的好方法。小黄瓜的潜在成本是用另一种语言工作的额外复杂性。正如我们上面提到的，我们用三种不同的语言表达共同的想法。*

*   *OpenAPI 规范——虽然 API 描述的语法可能是 YAML 或 JSON，但它是一种形式化行为的声明性语言。*
*   *小黄瓜——行为的另一种形式化。它没有 OpenAPI 规范严格。*
*   *代码——这仍然是期望行为的另一种形式化。对我来说，这是用 Python 写的。当然，它可以涉及其他语言，如 HTML、CSS、SQL、Jinja 模板语言等。*

## *开发过程*

****一旦我们让*** 将我们的 API 描述写成 OpenAPI 规范， ***我们就可以开始编码了。****

*一旦我们有了小黄瓜特性定义，我们就可以开始测试了。*

*一旦我们有了棒球和球棒，我们就可以打球了。*

*大部分工作是编写和重构代码，直到测试通过，代码看起来足够好可以发布。理想情况下，我们使用 Behave(或 pytest-bdd)来确认软件工作。配置存储库很方便，这样每次提交都会正常运行，并确认代码有效。在某些情况下，使用 Git pull 请求来触发运行 Behave 可能更简单。这确认了要合并的代码将满足其所需的行为。*

*当我们看到从规范到小黄瓜到代码的过程时，缩小超级灵活的小黄瓜描述的范围是很有帮助的，这样它们就能准确地匹配 API 描述。*

*这是我的偏好。*

1.  *将 API 描述写入 OpenAPI 规范。我喜欢从这里开始，因为它有助于我专注于 RESTful API 的限制和约束。*
2.  *将 API 描述转换成小黄瓜。我将在下面概述一个工具。一般来说，这是一个困难的问题，但是您可以很容易地用 Python 开发一个支持必要的定制和调整的工具。*
3.  *提供步骤定义，这样小黄瓜就可以用于运行测试。前几次，这涉及到一些学习。好的步骤定义可以足够通用，具有高度的可重用性。*
4.  *编写有效的代码。重构代码，直到它是好的。*

## *将开放 API 规范转换为小黄瓜*

*从编写到 OpenAPI 规范的 API 描述转移到 Gherkin 是将 OAS API 描述(或简称为规范)细节重新组织成不同的符号。在转换过程中避免损失和混乱是很重要的。*

*OpenAPI 规范的嵌套结构可以总结如下:*

```
*path:
    operation:
       … (request details)
       responses:
 code: … (response details)*
```

*一个规范通常有许多路径。对于每个路径项，都有一个或多个操作。每个操作都有一个或多个响应。*

*一个小黄瓜文档——基于写在 OpenAPI 规范中的 API 描述——将有一个(或多个)特性描述。(标签可用于区分复杂应用程序的功能。)每个特性将包括多个场景。每个场景描述了对(路径、操作)组合的一种响应。*

*场景的小黄瓜模板可以是这样的:*

```
*scenario: {operation summary} leading to {response description}
 Given {context}
 And some context tied to the response
 When {operation} on {path}
 And some reason for the response
 Then validate expected {response}*
```

*上面的大多数{占位符}直接来自 OAS 描述。例外情况是“与回应相关的背景”和“回应的原因”。*

*这些“一些上下文”和“一些原因”短语是细节的例子，这些细节在规范中是隐含的，而不是陈述的。发生这种情况是因为 OpenAPI 规范是特定于 RESTful API 的，并且所提供的例子关注于成功响应的“快乐之路”。401 未授权或 404 未找到的任何“不愉快”响应通常会在响应的描述属性中进行总结。这就是为什么我认为导致不愉快反应的条件是隐含的。*

*这里有一个具体的例子。我们将使用 OpenAPI 规范为我们编写的 API 描述添加一个路径，以便为仲裁人获取详细信息。这意味着一个未知裁判的失误。*

```
*/umpires/{umpire}:
  get:
    summary: An umpire
    parameters:
      — name: umpire
        in: path
        description: the umpire’s name
        type: string
        required: true
    responses:
      200:
        description: An umpire’s details
        schema:
          type: object
          properties:
            name:
              type: string
              example: “Tinker”
      404:
        description: Unknown umpire*
```

*这描述了当裁判未知时情况的响应状态代码 404。理解 RESTful API(和 OpenAPI 规范)的开发人员知道这意味着什么。当使用 Gherkin 步骤定义时，我们需要明确这一点，以确保我们能够区分这两种场景。*

*我们有几种方法来揭示这种错误的原因的细节:*

*   *尝试使用风格化的自然语言来扩展规范描述条目。添加错误的例子来定义这些额外的小黄瓜场景会使规范变得混乱。*
*   *在规范中以“x-given”和“x-when”的形式添加扩展属性。扩展属性必须被不能使用它们的工具悄悄忽略，所以添加它们是安全的。*

```
*404:
  description: Unknown umpire
  x-given: Umpires are [“Tinker”]
  x-when: request is for “Evers”*
```

*这些扩展值提供了构建小黄瓜错误场景所需的具体示例。这个想法可以扩展到提供许多错误场景。*

*虽然添加错误细节可以更容易地将编写到 OpenAPI 规范中的 API 描述转换为有意义的小黄瓜，但它们会使事情变得混乱。对快乐的路径使用 OpenAPI 规范似乎更好，并且只用小黄瓜描述不快乐的路径。*

*技术黄瓜*

*要完成从规范到小黄瓜再到代码的重要传递，还需要一个更重要的主题。我们必须稍微改变我们的关注点。小黄瓜——当被企业主使用时——就像棒球击球手挥棒击球。*这是战略性的一击:这将是一个全垒打。**

*小黄瓜可以缩小到有一个技术重点。这里有一个例子。*

```
*Scenario: Get list of Umpires
Given a test app and client
 And a valid Authorization token is ‘dizzy’
 And a test database
 And umpire names of [“Tinker”, “Evers”, “Chance”]
When headers include {“x-Auth-Token”: “dizzy”, “Accept”: “application/json”}
 And request operation is ‘Get’
 And request path is ‘/umpires’
Then response status is 200
 And response body is {“status”: “OK”, “data”: [“Tinker”, “Evers”,     “Chance”]}*
```

*小黄瓜场景的一般形状具有给定的时间结构。然而，细节已经从产品所有者的抽象观点变成了描述 RESTful API 实现的具体技术细节。*

*这些步骤已经使用一些样板文本“headers include ”(后跟参数值)进行了参数化。其思想是基于行为工具解析步骤字符串的能力来创建步骤定义。一个通用的 Python 步骤实现可以处理各种参数值。*

*以下是使用 Behave 符号的参数化步骤定义:*

```
*@when(‘Response status is {status:int}’)
def check_status(context, status):
    assert context[‘status’] == status*
```

*这个@when 定义可以被所有从我们为 OpenAPI 规范编写的 API 描述中生成的小黄瓜场景重用。拥有一个可重用的步骤定义库意味着对规范的更改将会改变基于小黄瓜的处理。这对于执行变更后的测试场景有直接的好处。*

*小黄瓜脚本的 OpenAPI 规范*

*虽然这看起来非常清楚和直接，但有许多复杂的因素。*

*   *命名。我们为 OpenAPI 规范编写的 API 描述允许对操作和每个响应进行总结和描述。如何使用这些字段的细节可能有所不同，每个组织将建立不同的命名约定。OpenAPI-to-Gherkin 工具必须反映组织的命名约定。*
*   *可选功能。操作的规范可以定义许多可选参数。描述文本可能包括一些关于参数含义和省略参数时的默认行为的指导。枚举场景可能很难自动化，可能需要手动干预来提供显示参数如何交互的详细测试用例。*
*   *当地标准。在每个 OpenAPI API 文档中，一个已建立的微服务家族可能会假定而不是定义一些标准特性。虽然可以将描述复制到每个 OpenAPI API 文档中，但是可能很难找出所有需要测试的以小黄瓜为中心的变体。在 OpenAPI 规范之外处理这些情况可能更好。*
*   *外部依赖性。当使用简单的微服务时，数据库的状态可以用 x-given 扩展来描述。试图通过简单的 x-given 扩展来描述具有多个复杂外部依赖的错误和问题可能会变得非常复杂。在这些情况下，一些手动创建的步骤定义可能比制定 OAS 扩展更容易。*
*   *不愉快的路径场景。与其用错误处理细节来混淆我们的 API 描述，不如用 OpenAPI-to-Gherkin 工具来注入错误处理场景。*

*一个简单而通用的 OpenAPI-to-Gherkin 应用程序似乎是不必要的困难。大多数实际应用都涉及扩展和可选功能。相反，建立一个定制的两步转换管道似乎更好。*

1.  *中的 **OpenAPI。这将从我们写给 OpenAPI 规范的 API 描述中构建场景定义。在获取源时，可以根据需要处理定制和扩展。***
2.  ***小黄瓜出来了。**这将从场景定义中写入小黄瓜特征文件。这涉及到步骤定义和小黄瓜文本之间的特性的精心编排。定制和扩展可以在这里注入。*

*在这两者之间是作为一系列场景的特性的内部表示。每个场景都是从 OpenAPI 文档中提取的值的集合，并重新组装到一个 Python 字典中，其关键字如下:*

*   ***路径-** 进入 When 步骤的路径。*
*   ***操作-** 进入 When 步骤的操作。*
*   ***请求-** 请求的细节和参数。其中一部分填充了场景名称。其中的一部分将填充 When 步骤，以发出请求。在有可选参数的情况下，每个组合可能导致一个单独的场景。注意这里的组合爆炸:a *n* 可选参数的幂集的大小是 2 *n* 。*
*   ***状态-** 这是该场景的总体最终状态。*
*   ***响应-** 这是预期的响应示例文档。*

*这里有一个例子:*

```
*{‘path’: ‘/credentials’,
 ‘operation’: ‘post’,
 ‘request’: {
     ‘parameters’: [
         {‘in’: ‘body’,
          ‘name’: ‘User Credentials’,
          ‘schema’: {
              ‘description’: ‘input’,
              ‘properties’: {‘username’: {‘type’: ‘string’}} }
         } 
        ],
       ‘summary’: ‘Post new user credentials’,
},
‘status’: ‘200’,
‘response’: {‘description’: ‘it worked’}}*
```

*是的，你是对的，这个参数没有任何例子细节。这只是一个模式。填写这些细节是初始质量检查的一部分。如果一个简单的工具不能生成合适的小黄瓜测试，那么规范就需要扩展。*

*创建它的基本 Python 编程具有以下类型的顶级函数:*

```
*def make_gherkin(oaspec: Dict, file: OptFile=sys.stdout): common = get_common_features(oaspec)
    emit_feature_header(oaspec, file)
        for scenario in scenario_iter(oaspec):
            emit_scenario(make_scenario(common, scenario), file)*
```

*输出由两个函数创建。*

*   *emit_feature_header()在文件的顶部写入“feature:”部分。编写特性头的模板很简单。*
*   *emit_scenario()为给定的场景编写小黄瓜步骤。这个也比较简单。这就是用“给定”、“何时”、“然后”和“和”这样的词来注释各个步骤的问题。*

*数据收集由三个功能完成:*

*   *get_common_features()从 OpenAPI 文档中提取公共细节。这包括安全信息、MIME 类型和安全定义。这将从 OpenAPI 文档中提取像 basePath 这样的字段。*
*   *make_scenario()从 OpenAPI 中提取细节，并将这些细节重新打包到各个小黄瓜步骤中。这是处理过程中最复杂的部分。*
*   *scenario_iter()是一个生成器函数，它基于路径、操作和可选参数的各种组合生成一系列场景。*

*scenario_iter()函数如下所示:*

```
*def scenario_iter(oaspec: Dict) -> Iterable[Dict]:
  for path in oaspec[‘paths’]:
    for operation in oaspec[‘paths’][path]:
        request = oaspec[‘paths’][path][operation].copy()
        # Optional: compute the powerset of the optional  parameters
        responses = request.pop(‘responses’)
        for response_status in responses:
            response = responses[response_status].copy()
            yield {
                ‘path’: path,
                ‘operation’: operation,
                ‘request’: request,
                ‘status’: response_status,
                ‘response’: response}*
```

*这个函数遍历路径、操作和响应。它将每个独特的组合生成一个独特的场景。细节可以用来以小黄瓜符号的形式显示细节。*

*这个例子没有计算可选参数的幂集。itertools 文档展示了如何使用 chain.from_iterable()函数构建 powerset()迭代器。当使用复杂的 OpenAPI 规范时，这可以为备选场景增加一个有用的复杂级别。*

*make_scenario()函数是大部分转换发生的地方。这个函数的目标是发出一个遵循 Gherkin 场景整体结构的字典。这是我们编写的 API 描述的隐式细节被扩展成显式小黄瓜文本的地方。*

*make_scenario()的结尾将如下所示:*

```
*scenario = {
    ‘scenario’: [summary, description],
    ‘given’: [],
    ‘when’: [],
    ‘then’: []
}
scenario[‘given’].append(‘test app and client’)
scenario[‘given’].extend( [f”{sec_key} security” for sec_key in security_keys] )

if req_header:
    req_header_json = json.dumps(req_header)
    scenario[‘when’].append(f”headers are {req_header_json}”)
if req_query:
    req_query_json = json.dumps(req_query)
    scenario[‘when’].append(f”query is {req_query_json}”)
if req_body:
    req_body_json = json.dumps(req_body)
scenario[‘when’].append(f”body is {req_body_json}”)
scenario[‘when’].append(f”request operation is {operation}”)
scenario[‘when’].append(f”request path is {basePath}{path}”)

scenario[‘then’].append(f”status is {status} {response[‘description’]}”)
if set(response.keys()) > {‘description’}:
    response_json = json.dumps(example_response)
    scenario[‘then’].append(f”response is {response_json}”)*
```

*三个小黄瓜步骤中的每一步都是一个子句列表。每个子句都是一个格式化的字符串，对人们来说有意义，对 behavior 或 pytest-bdd 的步骤匹配规则也有用。字符串的良好选择反映了技术细节和有意义的术语之间的平衡。*

*请注意，这些子句中的每一个都与阶梯夹具直接平行。例如，向 when 子句追加头是{…}"。“标题是”文本必须与步骤定义相匹配。我们可以使用像@ when(" header 是{…} ")这样非常详细的东西。如果我们使用@when(parsers.re(r "头是(？P*

*。*)")，因此通用步骤定义可以在多个场景中重用。*

*现在我们已经完成了从规范到小黄瓜的第一个接力，我们准备好了下一个从规范到小黄瓜再到代码的接力。像 Behave 和 pytest 这样的工具是裁判，做出这出戏成功的最终决定。*

## *规范到小黄瓜到代码*

*我们通常称项目大纲为“推销”让我们不要把这个类比推得太远，但是防守团队——教练、投手和捕手正在整理球场。当它被打到内场时，有很多防守会出错，让跑垒员绕垒前进。*

*按照 OpenAPI 规范编写 API 描述是设计 API 的第一步。这种描述是基于音高的，它可以驱动 API 设计和实现*。**

*虽然有许多针对编写到 OpenAPI 规范中的 API 描述的测试工具，但它们都有一个局限性:OpenAPI 示例只涵盖了快乐路径，而没有指定所有“其他”路径。如果我们编写自己的工具来从扩展的 OpenAPI 规范中创建测试，我们可以很容易地包含非快乐路径的例子。*

*像 behavior 这样基于小黄瓜的工具产生正式的文档，显示一套验收测试全部通过。如果我们没有通过所有测试的正式通知，我们就在没有任何官员的情况下进行运动。很好玩，但是纠纷不可避免。公正地评价临近的比赛真的很有帮助。*

*将球从规范扔向小黄瓜再扔向代码，可以很容易地确保这个剧本被正确调用。*

***相关:***

*   *[痛击——用 Python 替换 Shell 脚本](/capital-one-developers/bashing-the-bash-replacing-shell-scripts-with-python-d8d201bc0989)*
*   *[NoSQL 数据库并不意味着没有模式](/capital-one-developers/nosql-database-doesnt-mean-no-schema-a824d591034e)*
*   *[自动化 NoSQL 数据库构建](/capital-one-developers/automating-nosql-database-builds-a-python-to-the-rescue-story-that-never-gets-old-1d9adbcf6792)*

*[![](img/c6c5bb1f3967049ba012aebf5757e08d.png)](https://medium.com/capital-one-tech/api/home)*

**声明:这些观点仅代表作者个人观点。除非本帖中另有说明，否则 Capital One 不属于所提及的任何公司，也不被其认可。使用或展示的所有商标和其他知识产权都是其各自所有者的所有权。本文为 2018 首都一。**