# 使用 Easy-Fix 运行集成测试，如单元测试

> 原文：<https://medium.com/walmartglobaltech/use-easy-fix-to-run-integration-tests-like-unit-tests-834288ec446d?source=collection_archive---------5----------------------->

![](img/2b05486162ae795232f3f0dadbc8e056.png)

Alexas_Fotos (Public Domain) [https://pixabay.com/en/puzzle-drawing-background-696725/](https://pixabay.com/en/puzzle-drawing-background-696725/)

软件很复杂，所以测试很困难。为了使测试易于理解，工程师在不同类型的测试之间划分了职责。单元测试相对简单——特定的和粒度的，它们严格地以孤立的方法和功能为目标。集成测试关注软件组件之间的交互。对于 web 项目，这包括与 web 服务交互的 web 浏览器，尽管在本文中我们将只关注 web 服务。

与单元测试相比，集成测试范围更广，执行速度更慢。例如，它们可能“实时”(在测试环境中)运行，与远程系统交互或改变数据库。这种复杂性是有代价的:测试需要更多的时间和精力来设置、运行和理解它们何时失败。通过“嘲笑”集成测试来避免这种复杂性似乎很诱人。集成测试可以设计为使用从本地存储加载的经过策划的“模拟”数据运行，模拟与其他组件的交互。

现在还不清楚用模拟数据进行测试是否是个好主意。带有模拟数据的测试运行快速且一致，就像单元测试一样，这很有吸引力。然而，模拟集成测试实际上并不与远程系统交互——模拟数据限制了测试的范围。如果远程系统发生变化，模拟数据可能会变得陈旧，从而使测试结果无效，并可能对被测组件产生错误的信任。实时集成测试需要时间，但是当它成功时，它提供了目标软件组件实际上协同工作的真正信心。

# 不要在模拟和实时集成测试之间妥协

集成测试的两种方法都有优点，所以很容易把这看作一种权衡。然而，我们建议您不需要在集成测试中做出任何这样的妥协。在模拟测试和现场测试之间进行选择是一个错误的选择——可以编写集成测试，使它们可以以任何一种方式运行。此外，我们很高兴引入一个新的 nodejs 模块，“[easy-fix](https://github.com/walmartlabs/easy-fix)”(【https://github.com/walmartlabs/easy-fix】)鼓励这种不妥协的集成测试方法。Easy-fix 简化了一组可以实时运行的集成测试，捕获在目标组件之间传递的数据，然后可以用捕获的(模拟)数据重放相同的测试。

简单修复的工作方式如下:

*   编写您的集成测试，使它们能够实时运行，允许网络请求、数据库突变等；
*   用 easy-fix 中的一个方法包装会产生副作用的异步任务。
*   运行这些测试，它们有一个新的选项:
*   在“实时”模式下，你的测试就像你写的那样运行，允许有副作用。
*   在“捕获”模式下，您的测试像“实时”模式一样运行，但是每个包装任务的参数和响应被序列化并保存。
*   在“重放”模式下，不调用包装的任务。相反，它们用保存的(模拟)数据进行响应。

总之，easy-fix 提供了一种简单的方法来获取测试数据，并将这些数据反馈到测试中。

# 用法示例

下面是一个使用 easy-fix 的代码示例。在测试设置方法中，我们使用 easy-fix 来包装一些异步任务。当然，测试作者决定应该包装什么任务。例如，为了测试一个 walmart.com 收银台，我们可能会包装验证请求、获取费用和税收细节等内部任务。

```
before( function () {
  easyFix.wrapAsyncMethod(helpers, 'authenticate');
  easyFix.wrapAsyncMethod(isd.IroRequest.prototype, 'getProductOffers');
  easyFix.wrapAsyncMethod(isd.TaxRateRequest.prototype, 'getTaxRates');
  easyFix.wrapAsyncMethod(restrictions, 'getRestrictonsForUpcs');
  easyFix.wrapAsyncMethod(productFees, 'getFeesForUpcs');
});
```

实际的测试代码不需要为了容易修复而修改。walmart.com 结帐的测试可能如下所示:

```
it('checkout one item', function (done) {
  attemptCheckout(
    testUser,
    checkoutDetails,
    function (err, resp, body) {
      var bodyJson;
      expect(err).to.be.null;
      expect(resp.statusCode).to.equal(200);
      bodyJson = typeof (body) === 'string' ? JSON.parse(body) : body;
      expect(bodyJson.data).to.exist;
      expect(bodyJson.error).to.not.exist;
      done();
    }
  );
});
```

注意，测试代码没有显示出任何易修复的意识。但是，当我们执行测试时，$TEST_MODE 环境变量将决定测试是否与远程系统交互。在“实时”和“捕获”模式下，结帐服务可以发出相关的网络请求，并将结帐记录在数据库中。在“重放”模式下，测试不做这些，只使用假的模拟数据运行。

```
➜  export TEST_MODE=live
➜  mocha test/int/endpoints-with-auth.js -t 20000 -g 'checkout one item' ✓ checkout one item - UPCE (12704ms) 1 passing (16s)➜  export TEST_MODE=capture
➜  mocha test/int/endpoints-with-auth.js -t 20000 -g 'checkout one item' ✓ checkout one item - UPCE (14311ms) 1 passing (17s)➜  export TEST_MODE=replay
➜  mocha test/int/capture.js test/int/endpoints-with-auth.js -g 'checkout one item' ✓ checkout one item - UPCE (71ms) 1 passing (224ms)
```

在沃尔玛实验室，在商店服务团队，我们开始编写集成测试来运行。一个包含几十个集成测试的示例测试套件需要大约两分钟来运行。这并不是世界末日，但它足够慢，工程师通常只在考虑提交新代码时才运行它们。集成 easy-fix 后，这些测试在 4 秒钟内运行(在“重放”模式下)。巨大的速度提升使得工程师可以更频繁地运行测试(在重放模式下)——比如每次文件保存。

# 快速但不神奇

乍一看，这似乎是一个不可思议的改进——我们的示例测试套件的执行时间从 120 秒减少到了 4 秒。然而，当你考虑测试模式之间的差异时，速度上的差异似乎并不令人惊讶。在“实时”或“捕获”模式下运行的测试包括目标组件之间的交互。它们运行起来相对较慢，但是提供了这些组件协同工作的信心。

“重放”模式下的测试使用模拟数据，通常只测试目标交互的一个方面。实际上，重放模式下的测试仍然会捕获许多(但不是全部)错误。快速执行允许它们更频繁地运行，在编程过程中更早地发现许多错误。

从概念上讲，easy-fix 是另一个录音回放系统，这并不新鲜。有几个这样做的其他项目的例子，例如

*   [棕褐色](https://github.com/linkedin/sepia)
*   [录像机](https://www.npmjs.com/package/vcr)
*   [诺克-录像机](https://github.com/carbonfive/nock-vcr)
*   [节点回放](https://github.com/assaf/node-replay)

但是，所有这些例子都只关注模拟 HTTP 调用。奇怪的是，我们找不到任何适用于其他异步任务的现有录音回放系统，这就是我们创建 easy-fix 的原因。

明确地说，关键的区别在于 easy-fix 将包装任何异步 nodejs 方法。Easy-fix 可以模拟 HTTP 调用、数据库访问或任何可以序列化请求和响应的内容。

软件测试仍然很复杂，但是 easy-fix 提供了一个简单、有用的简化方法。有了 easy-fix，您不需要在模拟或实时集成测试之间妥协。

易于修复的源代码可从 https://github.com/walmartlabs/easy-fix[的](https://github.com/walmartlabs/easy-fix)获得