# 你不知道的 JavaScript：入门 —— 第 2 版
# 附录 A：进一步探讨

在这个附录中，我们将更详细地探讨正文中的一些话题。可以把这些内容看作是对本系列其余部分所涉及的一些细节的可选预览。

## 值与引用

在第 2 章中，我们介绍了两种主要类型的值：原始和对象。但我们还没有讨论两者之间的一个关键区别：如何赋值和传递这些值。

在许多语言中，开发者可以选择将一个值的本身或它的引用用来赋值/传递。但在 JS 中，这个决定完全是由值的种类决定的。这让很多来自其他语言的开发者在开始使用 JS 时感到惊讶。

如果你赋值/传递一个值的本身，这个值就会被复制。比如说：

```js
var myName = "Kyle";

var yourName = myName;
```

在这里，`yourName` 变量中的 `"Kyle"` 字符串副本与存储在 `myName` 中的值不同。这是因为该值是一个原始值，而原始值总是作为**值的副本**来赋值/传递。

下面是如何证明有两个不同的值：

```js
var myName = "Kyle";

var yourName = myName;

myName = "Frank";

console.log(myName);
// Frank

console.log(yourName);
// Kyle
```

看到了吗？`yourName` 没有受到 `myName` 重新赋值为 `"Frank"` 的影响。这是因为每个变量拥有值的副本。

相比之下，引用是指两个或多个变量都指向同一个值，所以对这个共享值的修改会在任何一个引用的访问中被反映出来。在 JS 中，只有对象值（数组、对象、函数等）才会被当作引用。

考虑：

```js
var myAddress = {
    street: "123 JS Blvd",
    city: "Austin",
    state: "TX"
};

var yourAddress = myAddress;

// 我得搬到新房子去了！
myAddress.street = "456 TS Ave";

console.log(yourAddress.street);
// 456 TS Ave
```

因为赋给 `myAddress` 的值是一个对象，它是通过引用来持有/赋值的，因此赋值给 `yourAddress` 变量的是引用的副本，而不是对象值本身。这就是为什么当我们访问 `yourAddress.street` 时，会反映出赋值给 `myAddress.street` 的更新值。`myAddress` 和 `yourAddress` 各有一个共享对象的引用的副本，所以对其中一个的更新就是对两个的更新。

同样，JS 根据值的类型选择值复制与引用复制的行为。原始值是通过值来持有，对象是通过引用来持有。在 JS 中，无论用什么方式，都没有办法覆盖这一点。

## 这么多的函数形式

回忆一下第 2 章 "函数" 部分的这个片段：

```js
var awesomeFunction = function(coolThings) {
    // ..
    return amazingStuff;
};
```

这里的函数表达式被称为*匿名函数表达式*，因为它在 `function` 关键字和 `(..)` 参数列表之间没有名称标识符。这一点让很多 JS 开发者感到困惑，因为从 ES6 开始，JS 对匿名函数进行了 "名称推断"：

```js
awesomeFunction.name;
// "awesomeFunction"
```

函数的 `name` 属性将显示其被直接赋予的名称（在声明的情况下）或在匿名函数表达式的情况下推断出来的名称。这个值通常被开发工具在检查函数值或报告错误堆栈跟踪时使用。

因此，即使是一个匿名函数表达式也*可能*得到一个名称。然而，名称推断只发生在有限的情况下，例如当函数表达式被赋值时（用 `=`）。例如，如果将一个函数表达式作为参数传递给函数调用，则不会发生名称推断；`name` 属性将是一个空字符串，开发者控制台通常会报告 “(anonymous function)”。

即使推断出了名称，**它也是一个匿名函数。** 为什么？因为推断的名称是一个元数据字符串值，而不是可用来引用函数的标识符。一个匿名函数没有一个标识符用来从自身内部引用自己 —— 用于递归、事件解除绑定等。

与匿名函数表达式比较：

```js
// let awesomeFunction = ..
// const awesomeFunction = ..
var awesomeFunction = function someName(coolThings) {
    // ..
    return amazingStuff;
};

awesomeFunction.name;
// "someName"
```

这个函数表达式是一个*已命名函数表达式*，因为标识符 `someName` 是在编译时直接与函数表达式相关联的；而与标识符 `awesomeFunction` 相关联还是要到该语句的运行时才会发生。这两个标识符不一定要匹配，有时让它们不同是有意义的，其他时候让它们相同是更好的。

还请注意，在为 `name` 属性赋值*名称*时，显式函数名，即标识符 `someName`，是优先的。

函数表达式应该命名还是匿名？在这个问题上，人们的看法大相径庭。大多数开发者倾向于不介意使用匿名函数。它们更短，而且毫无疑问，在 JS 代码中更常见。

在我看来，如果一个函数存在于你的程序中，它就有一个目的，否则，就把它拿掉吧！如果它有一个目的，它就有一个合理的名字来描述这个目的。

如果一个函数有名字，你这位代码作者应该在代码中加入这个名字，这样读者就不必通过阅读和在心里执行该函数的源代码来推断这个名字。即使是像 `x * 2` 这样细小的函数体，也要通过阅读来推断出 “double” 或 “multBy2” 这样的名称；如果你能花一秒钟的时间将函数命名为 “double” 或 “multBy2” *一次*，就可以省去读者今后每次阅读时重复的脑力劳动，这种短暂的额外脑力劳动是不必要的。

遗憾的是，在某些方面，截至 2020 年初，JS 中还有很多其他的函数定义形式（也许将来会有更多！）。

以下是更多的声明形式：

```js
// 生成器函数声明
function *two() { .. }

// 异步函数声明
async function three() { .. }

// 异步生成器函数声明
async function *four() { .. }

// 已命名函数导出声明（ES6 模块）
export function five() { .. }
```

这里还有一些（很多！）函数表达式形式：

```js
// IIFE
(function(){ .. })();
(function namedIIFE(){ .. })();

// 异步 IIFE
(async function(){ .. })();
(async function namedAIIFE(){ .. })();

// 箭头函数表达式
var f;
f = () => 42;
f = x => x * 2;
f = (x) => x * 2;
f = (x,y) => x * y;
f = x => ({ x: x * 2 });
f = x => { return x * 2; };
f = async x => {
    var y = await doSomethingAsync(x);
    return y * 2;
};
someOperation( x => x * 2 );
// ..
```

请记住，箭头函数表达式是**语义匿名**的，这意味着语法没有提供一种为函数直接提供名称标识符的方法。函数表达式可能会得到一个推断的名称，但只有当它是赋值形式之一时，而不是以（更常见的！）被传递为函数调用参数的形式（如代码片段的最后一行）。

由于我认为匿名函数在你的程序中并不是一个经常使用的好主意，所以我不喜欢使用 `=>` 箭头函数形式。这种函数其实有特定的用途（即用词法处理 `this` 关键字），但这并不意味着我们写的每一个函数都应该使用它。为每项工作使用最合适的工具。

函数也可以在类定义和对象字面量定义中指定。当它们以这些形式出现时，通常被称为 “方法”，尽管在 JS 中，这个术语与 “函数” 并没有太多可观察的区别：

```js
class SomethingKindaGreat {
    // 类方法
    coolMethod() { .. }   // 没有逗号！
    boringMethod() { .. }
}

var EntirelyDifferent = {
    // 对象方法
    coolMethod() { .. },   // 逗号！
    boringMethod() { .. },

    //（匿名）函数表达式属性
    oldSchool: function() { .. }
};
```

唉！这就是定义函数的很多不同方式。

这里没有简单的捷径，你只需要建立对所有函数形式的熟悉，这样你就可以在现有的代码中识别它们，并在你写的代码中适当地使用它们。仔细研究它们，并进行练习！

## 强制性条件比较

是的，这一节的名称很拗口。但是我们在说什么呢？我们说的是条件表达式需要进行强制性比较来做决定。

`if` 和 `? :` 三元语句，以及 `while` 和 `for` 循环中的检测子句，都会执行隐式的值的比较。但是什么样的比较呢？是 “严格的” 还是 “强制性的”？其实都有。

考虑：

```js
var x = 1;

if (x) {
    // 将会运行！
}

while (x) {
    // 将会运行一次！
    x = false;
}
```

你可能会认为这些 `(x)` 条件表达式是这样的：

```js
var x = 1;

if (x == true) {
    // 将会运行！
}

while (x == true) {
    // 将会运行一次！
    x = false;
}
```

在这一特定情况下 —— `x` 的值是 `1` —— 这种思维模型是有效的，但从更广泛的角度看，它并不准确。请考虑：

```js
var x = "hello";

if (x) {
    // 将会运行！
}

if (x == true) {
    // 不会运行 :(
}
```

糟糕。那么，`if` 语句到底在干什么？这是比较准确的思维模型：

```js
var x = "hello";

if (Boolean(x) == true) {
    // 将会运行
}

// 和下面一样：

if (Boolean(x) === true) {
    // 将会运行
}
```

由于 `Boolean(..)` 函数总是返回一个布尔类型的值，所以这个片段中的 `==` 与 `===` 无关，它们都会做同样的事情。但重要的是要看到在比较之前，发生了强制，`x` 从当前的类型，转换到布尔类型。

在 JS 比较中，你离不开强制。专注并学会它们。

## 原型 “类”

在第 3 章中，我们介绍了原型，并展示了我们如何通过原型链连接对象。

另一种连接原型的方式是优雅的 ES6 `class` 系统（见第 2 章，“类”）的前身（说实话，很丑），被称为原型类。

| 提示： |
| :--- |
| 虽然这种风格的代码在现在的 JS 中并不常见，但令人费解的是在求职面试中被问到是相当常见的！|

我们先来回顾一下 `Object.create(..)` 的编码风格：

```js
var Classroom = {
    welcome() {
        console.log("Welcome, students!");
    }
};

var mathClass = Object.create(Classroom);

mathClass.welcome();
// Welcome, students!
```

在这里，一个 `mathClass` 对象通过其原型连接到一个 `Classroom` 对象。通过这种连接，函数调用 `mathClass.welcome()` 被委托给 `Classroom` 上定义的方法。

原型类模式会将这种委托行为标记为 “继承”，或者将其定义为（具有相同的行为）：

```js
function Classroom() {
    // ..
}

Classroom.prototype.welcome = function hello() {
    console.log("Welcome, students!");
};

var mathClass = new Classroom();

mathClass.welcome();
// Welcome, students!
```

所有函数默认都会在一个名为 `prototype` 的属性中引用一个空对象。尽管命名很混乱，但这并**不是**函数的*原型*（函数原型连接到的地方），而是当用 `new` 调用函数创建其他对象时要*连接*到的原型对象。

我们在该空对象上添加一个 `welcome` 属性（称为 `Classroom.prototype`），指向 `hello()` 函数。

然后 `new Classroom()` 创建一个新的对象（赋值给 `mathClass`），将其原型连接到现有的 `Classroom.prototype` 对象上。

虽然 `mathClass` 没有 `welcome()` 属性/函数，但它成功地委托给了 `Classroom.prototype.welcome()` 函数。

现在强烈不建议使用这种 “原型类” 模式，而是使用 ES6 的 `class` 机制：

```js
class Classroom {
    constructor() {
        // ..
    }

    welcome() {
        console.log("Welcome, students!");
    }
}

var mathClass = new Classroom();

mathClass.welcome();
// Welcome, students!
```

表面之下，同样是原型连接，但这种 `class` 语法比 “原型类” 更符合面向类的设计模式。
