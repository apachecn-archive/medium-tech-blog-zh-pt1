# Swift 中的属性包装器

> 原文：<https://medium.com/globant/property-wrappers-in-swift-85d2b2cc8b2?source=collection_archive---------0----------------------->

Swift 5.1 中引入了属性包装器，以消除样板代码，促进代码重用，并支持更具表现力的 API。

## 为什么我们需要属性包装器？

属性包装在管理属性存储方式的代码和定义属性的代码之间添加了一层隔离。

例如，如果您有提供线程安全检查的属性，或者将它们的底层数据存储在数据库中，那么您必须在每个属性上编写代码。

使用属性包装时，只需在定义包装时编写一次管理代码，然后通过将该管理代码应用于多个属性来重用它。

使用属性包装器是一种选择。但是一旦你意识到属性包装器及其好处，你就会理解它对 Swift 语言的补充。

![](img/f70433b2d8afa5877f87d83749032057.png)

PC: @[zlucerophoto](https://unsplash.com/photos/qAriosuB-lY)

> 这是对 Swift 库的一个很好的补充，它允许删除许多样板代码，这些代码我们可能都在项目中写过。

## 什么是属性包装？

若要定义属性包装，请创建一个定义 wrappedValue 属性的结构、枚举或类。

Swift 中的属性包装器允许您在不同的包装器对象中提取公共逻辑。

您可以将属性包装器视为一个额外的层，它定义了如何在读取时存储或计算属性。

受**科特林的委托属性**的启发，属性包装器最初被命名为**属性委托**。

## 剖析属性包装器

要定义属性包装器，可以使用注释 **@propertyWrapper**

属性包装器就像 Swift 中的任何其他结构一样，用名为 **wrappedValue** 的 **@propertyWrapper** 属性&进行注释。

**wrappedValue** 获取和设置块是您可以截取和执行所需操作的地方。

为了使用属性包装器，我们将为变量加上前缀 **@ <属性包装器名称>**

## 属性包装器内部是如何工作的？

每次编译器遇到属性包装时，它都会生成代码，为属性包装提供存储并通过属性包装访问属性。

结构、枚举或类定义属性，但为属性提供存储的是属性包装。顾名思义，属性由属性包装器包装。

## 访问包装的值

让我们看看一些代码👀

我们将实现一个 Base64Encoding 属性包装器，它将接受一个作为字符串的正常值，当我们读取该值时，它将总是返回一个 Base64 编码的字符串。

让我们了解一下`Base64Encoding`属性包装器的实现。我们有一个 String 类型的私有变量属性&和一个 getter & setter 的*包装值*。

getter 通过将值编码为 base64EncodingString()来返回存储在`value`属性中的值。setter 将新值赋给`value`属性。`wrappedValue`计算属性充当私有`value`属性的代理。

要使用 Base64Encoding 属性包装器，我们只需要用`@Base64Encoding`属性注释文本属性。

瞧啊。完成了。💃🏻

现在，每当我们从有效负载中访问文本时，它总是会返回一个 base64 编码的字符串。

## 从属性包装中投影值

属性包装可以通过定义投影值来公开附加功能。

预计值的名称与包装值相同，只是它以美元符号($)开头。

让我们用一个例子来形象地说明这一点:

在上面的 OddOrEven 示例中，代码将一个 *projectedValue* 属性添加到 *OddOrEven* 结构中，以跟踪数字是偶数还是奇数。

*数字结构。$someNumber* 访问包装器的预计值。

*numberStruct 的值。$someNumber* 为真，因为存储的数字是 6，它是偶数&，打印的消息是“*数字是偶数*”。但是，由于存储的数字是 *7* ，即*奇数* &，打印的信息是“*数字是奇数*”。

属性包装器可以返回任何类型的值作为它的投影值。这里，projectedValue 是 Bool 数据类型。

## 对属性包装本身的访问

要访问属性包装器类型本身，您需要向属性添加一个*下划线* ( _)。

这里，我们添加了额外的功能来检查小于 10 的数字。

这里，_someNumber 提供了对 OddOrEven 包装器的访问，因此我们可以调用 checkLessThanTen()方法。

但是，从外部调用包装器会产生编译错误。

然而，使用 projectedValue 可以公开 *checkLessThanTen* 方法。
通过向属性添加语法糖美元符号($)，可以公开包装器的实例。

包装器可以返回 *self* 来公开包装器的实例作为其投影值。

OddOrEven 类型的 projectedValue 正在返回 self 以公开 *OddOrEven* 包装器的实例。

现在，可以从外部调用包装器& checkLessThanTen 方法调用成功。

## 结论

简单地说，当我们需要约束属性值以应用某些逻辑或功能时，我们可以使用属性包装器。
属性包装器可以应用于局部存储变量，但不能应用于全局变量或计算变量。

属性包装器对于减少代码重复、提高可读性以及创建便于代码重用的表达性 API 非常有用。

## 参考资料:

[https://docs . swift . org/swift-book/language guide/properties . html](https://docs.swift.org/swift-book/LanguageGuide/Properties.html)