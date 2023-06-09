# 为 ES2015 解构主义干杯

> 原文：<https://medium.com/google-developer-experts/a-toast-to-es2015-destructuring-5ac639a666cf?source=collection_archive---------2----------------------->

![](img/08b824e33f4572faf5dd79c77721bf65.png)

我认为 [ES2015 析构语法](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)非常酷。它可能不会像承诺、生成器或类语法那样占据头条，但我发现它真的很有用。当你仔细阅读时，它也是[惊人的详细。](http://exploringjs.com/es6/ch_destructuring.html)

唯一遗憾的是，我发现的大多数例子都只关注语法，而不是现实生活中的用法。比如这个来自 [MDN 文档](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)的:

```
var a, b;
[a, b] = [10, 20];
console.log(a); // 10
console.log(b); // 20
```

幸运的是，我这里有一个真实生活中的用例，可以给你解构。

# 信守承诺

我最喜欢的析构用法之一是在使用`Promise.all`等待大量异步任务完成的时候。`Promise.all`的结果作为一个数组返回，你通常可以迭代它或者手工挑选出你需要的结果。

但是，如果您知道您期望接收什么对象，那么您可以使用参数析构来简化您的工作，并且通过预先命名参数来使您的代码更漂亮、更具描述性。让我们看一个例子。

# 比较啤酒

假设你想比较两种 [BrewDog 的美味啤酒](https://www.brewdog.com/beer/headliners)，这是我发现自己在现实生活中一直在做的事情。我们可以从山姆·梅森的名为[的朋克 API](https://punkapi.com/) 中获得关于他们的信息。为了实现这一点，我们使用`fetch` API 从 API 中获取每种啤酒的数据。我们需要先解决这两个请求，然后才能比较啤酒。

让我们看一下代码:

```
const punkAPIUrl = "[https://api.punkapi.com/v2/beers/106](https://api.punkapi.com/v2/beers/106)";
const deadPonyClubUrl = "[https://api.punkapi.com/v2/beers/91](https://api.punkapi.com/v2/beers/91)";
const punkAPIPromise = fetch(punkAPIUrl)
  .then(res => res.json())
  .then(data => Promise.resolve(data[0]));
const deadPonyClubPromise = fetch(deadPonyClubUrl)
  .then(res => res.json())
  .then(data => Promise.resolve(data[0]));Promise.all([punkAPIPromise, deadPonyClubPromise])
  .then(beers => {
    const punkIPA = beers[0];
    const deadPonyClub = beers[1];
    const stronger = (punkIPA.abv < deadPonyClub.abv ? deadPonyClub.name : punkIPA.name) + " is stronger";
    console.log(stronger);
  });
```

我们可以用参数析构来整理这个承诺:

```
Promise.all([punkAPIPromise, deadPonyClubPromise])
  .then(([punkIPA, deadPonyClub]) => {
    const stronger = (punkIPA.abv < deadPonyClub.abv ? deadPonyClub.name : punkIPA.name) + " is stronger";
    console.log(stronger);
  });
```

我们知道我们得到了两种啤酒，这个例子的构造方式，我们甚至知道哪种啤酒是哪种。因此，我们可以使用参数析构来命名数组中的啤酒，而不是取出它们。

# 更多示例

好吧，这看起来仍然像是一个虚构的例子，但它确实更接近现实生活。我第一次发现自己使用这种技巧是在为圣诞节的 12 个 dev 写关于服务人员的文章时。当编写使用`caches`和`fetch`API 实现“在重新验证时过时”缓存方法的方法`returnFromCacheOrFetch`时，它就派上了用场。

该方法打开一个命名的缓存，并尝试将当前请求与缓存进行匹配。在返回之前，它启动对所请求资源的`fetch`请求，缓存结果。最后，如果在缓存中找到请求，则返回缓存的响应，否则返回新的获取请求。你可以在[原博文](http://12devsofxmas.co.uk/2016/01/day-9-service-worker-santas-little-performance-helper/)中了解更多。

最终的代码如下所示:

```
function returnFromCacheOrFetch(request, cacheName) {
  const cachePromise = caches.open(cacheName);
  const matchPromise = cachePromise
    .then((cache) => cache.match(request)); // Use the result of both the above Promises to return the 
  // Response. Promise.all returns an array, but we destructure that
  // in the callback. 
  return Promise.all([cachePromise, matchPromise])
    .then(([cache, cacheResponse]) => { 
      // Kick off the update request
      const fetchPromise = fetch(request)
        .then((fetchResponse) => {
          // Cache the updated file and then return the response
          cache.put(request, fetchResponse.clone());
          return fetchResponse; 
        }); 
      // return the cached response if we have it, otherwise the 
      // result of the fetch. 
      return cacheResponse || fetchPromise;
    });
}
```

在这种情况下，我需要`caches.open`和`cache.match(request)`的结果来执行后台获取并返回缓存的响应。我用`Promise.all`把它们画在一起，然后解构得到的数组，保持代码的整洁和描述性。

# 命名事物

在这些例子中，参数析构允许我们命名我们期望从传递给`Promise.all`的解析承诺中得到的结果。事实上，在任何我们使用参数析构的地方，特别是数组，它允许我们更早更好地命名对象。从长远来看，这反过来又使代码更具可读性和可维护性。

您发现 ES2015 析构语法在代码的其他地方有用吗？我也很想知道你是如何使用这个特性的，所以请在 Twitter 上与我分享你的析构技巧。

[*干杯 ES2015 解构*](http://philna.sh/blog/2017/02/09/toast-to-es2015-destructuring/) *原载于 2017 年 2 月 9 日*[*philna . sh*](http://philna.sh/blog/2017/02/09/toast-to-es2015-destructuring/)*。*