# 如何学习 React #11:理解 React 形式的简单公式

> 原文：<https://medium.com/quick-code/how-to-learn-react-11-the-simple-formula-to-understand-react-form-ca5e0869910b?source=collection_archive---------0----------------------->

![](img/169ddc681d5c7de65d9ffe77087409fc.png)

你好，欢迎回来。首先我要祝贺你。你完成了 10 章。如果没有，请不要犹豫，在这里浏览它们[。如果你已经玩过我们的应用程序，你肯定会注意到它有一些问题。我找到了其中的两个](/@bernardbad)

1.  *用户可以添加名称为空的餐*
2.  *如果已经存在同名餐，用户可以将餐添加到列表中*

在本章中，我们将解决这些问题，为了不浪费时间，让我们继续解决第一个问题。所以我们有几种方法。如果膳食输入为空，我们可以允许用户点击按钮事件，并向他显示消息，他应该在提交前首先设置膳食名称。或者，如果输入为空，我们可以禁用该按钮。我个人喜欢第二个，所以我们要用那个。

在 HTML 中禁用按钮就像添加`disabled`属性一样简单

```
<button disabled="true"></button>
```

该属性可以是 true 或 false。好的，这应该不成问题。我们将输入的值存储在`this.state.meal`中，因此如果`this.state.meal`为空字符串，我们只需要将`disabled`设置为真。我们可以按照下列方式做

```
disabled={this.state.meal === ""}
```

如果`this.state.meal`是空字符串，我们的表达式将被赋值为`true`，而`disabled`将被赋值为`true`。其他情况下会是`false`。

很好…不需要做很多工作，我们已经解决了第一个问题。让我们来看第二个。提醒一下，问题是如果同名的餐已经存在，用户可以将餐添加到列表中。

我们可以使用类似的模式，并禁用按钮，如果该餐已经存在于列表中，但它没有太大的意义。我认为更好的解决方案是让用户知道这顿饭已经在列表中了。也许他只是在匆忙中忘记了他已经加了这顿饭…谁知道呢。逻辑应该是这样的。当用户提交表单时，我们将检查该餐是否已经在列表中。如果不是，我们将添加它。如果已经是了，我们就不添加了，我们将向用户发送信息。

让我们首先确保我们只添加不存在的食物。为此，我们必须稍微改变一下`handleSubmit`方法。我们只需要增加一个条件。我们检查`this.state.meal`是否出现在`this.state.meals`数组中，我们可以通过调用`this.state.meals.indexOf(meal) === -1`来完成。只是提醒一下`indexOf`是如何运作的。如果存在，它返回该项在数组中的位置，如果不存在，它返回`-1`。继续添加这个检查，然后`handleSubmit`看起来会像这样

现在我们只需要向用户显示消息。当添加新的食物时，我们已经在显示消息。我们为此准备了`this.state.showAddMealMessage`。现在，我们可以用名称`showErrorMesage`向我们的状态添加新的属性，但是我不认为我们需要这样做。我认为更好的方法是只有属性`this.state.showMessage`，作为一个值，我们将设置我们想要显示给用户的消息。在渲染中，我们将检查`this.state.showMessage`中是否有值，如果有，我们将显示给用户。否则我们什么都不会做。所以，是的……一点点重构，我们的`MealPlan`组件看起来会像这样

要总结组件中发生的变化:

*   `this.state.showAddMealMessage`更名为`this.state.showMesage`
*   在`render`中，我们检查`this.state.showMessage`中是否有值，如果有，我们将显示它。
*   增加了函数`showMessage`，该函数只为`this.state.showMessage`设置值，导致组件重新渲染，从而显示实际消息

您可能不熟悉的一个语法是在`render`中使用的`this.state.showMessage || null`。基本上，这意味着如果`this.state.showMessage`中有值，就显示出来，如果没有，就什么也不显示。

好的，让我们继续玩这个应用程序。看起来 bug 已经修复了。不要害羞…为自己鼓掌，如果你喜欢这个故事，你也可以为这个故事鼓掌，让世界知道你的成就。除了感谢你对我的包容，我没有别的话要说。如果你没有读过这个系列之前的故事，一定要在这里查看一下。如果你有什么意见，欢迎在评论区提问。我很乐意回答你的任何问题。敬请关注下一个系列，我们将为我们的应用程序添加新功能。那里见！