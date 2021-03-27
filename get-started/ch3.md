# 你不知道的 JavaScript：入门 —— 第 2 版
# 第 3 章：深入 JS 的根基

如果你已经阅读了第 1 章和第 2 章，并花时间去消化和吸收，希望你已经对 JS *有了*更多的了解。如果你跳过/略过了它们（尤其是第 2 章），我建议你回去花更多的时间来学习这些资料。

在第 2 章中，我们在高层次上调查了语法、模式和行为。在这一章中，我们的注意力转移到 JS 的一些低层次特性上，这些特性几乎支撑着我们编写的每一行代码。

请注意：这一章挖掘的内容可能比你平时思考一门编程语言时要深得多。我的目标是帮助你理解 JS 的核心工作原理，是什么让它变得如此有趣。本章会开始回答一些 “为什么？” 的问题，这些问题可能在你探索 JS 时出现。然而，这些资料仍然不是对这门语言的详尽论述；那是本系列丛书其他部分的目的！我们在这里的目标仍然只是为了*入门*，并且更加适应 JS 的*感觉*，了解它的来龙去脉。

不要快速读完这些资料，以至于迷失在其中。我已经说了很多次了，**慢慢来**。即使如此，你可能还是会带着很多问题读完这一章。这没关系，因为前面还有一整系列的书等着你继续探索！

## 迭代

由于程序本质上是为了处理数据（并对这些数据做出决策）而构建的，因此用于逐步处理数据的模式对程序的可读性有很大影响。

迭代器模式已经存在了几十年，它提出了一种 “标准化” 的方法，从数据源一次消耗一*块*数据。这个想法是，对数据源进行迭代 —— 先处理第一部分，然后是下一部分，以此类推，逐步处理数据，而不是一次性处理整个集合，这样更通用，也更有帮助。

想象一个数据结构表示一个关系型数据库 `SELECT` 查询，它通常将结果组织成行。如果这个查询只有一行或几行，你可以一次性处理整个结果集，并将每行赋值给一个局部变量，并对该数据执行任何合适的操作。

但如果查询有 100 或 1000（或更多！）行，你就需要迭代处理这些数据（通常是循环）。

迭代器模式定义了一个称为 “迭代器” 的数据结构，它有一个对数据源（如查询结果）的引用，它暴露了一个 `next()` 的方法。调用 `next()` 返回下一条数据（即数据库查询中的 “记录” 或 “行”）。

你并不总是知道需要迭代多少条数据，所以一旦你迭代完整个集合并*超过终点*，该模式通常会通过一些特殊的值或异常来表示完成。

迭代器模式的重要性在于坚持一种*标准的*数据迭代处理方式，这导致了更干净、更容易理解的代码，而不是让每个数据结构/源定义自己的数据处理方式。

经过多年来 JS 社区围绕着相互认可的迭代技术所做的各种努力，ES6 直接在语言中标准化了迭代器模式的特定协议。该协议定义了一个 `next()` 方法，其返回的是一个称为*迭代器结果*的对象；该对象具有 `value` 和 `done` 属性，其中 `done` 是一个布尔值，在对数据源的迭代完成之前都是 `false`。

### 使用迭代器

有了 ES6 的迭代协议，每当消耗一个数据源的值，也就是 `next()` 调用后，检查 `done` 是否为 `true` 以停止迭代是可行的。但是这种方法比较手动，所以 ES6 也包含了几种机制（语法和 API）来对这些迭代器的使用进行标准化。

其中一个机制就是 `for..of` 循环：

```js
// 给定某个数据源的迭代器：
var it = /* .. */;

// 一次一个地循环处理其结果
for (let val of it) {
    console.log(`Iterator value: ${ val }`);
}
// Iterator value: ..
// Iterator value: ..
// ..
```

| 注意： |
| :--- |
| 我们在这里省略了等价的手动循环，但它的可读性肯定比 `for..of` 循环要差！ |

另一个经常用于使用迭代器的机制是 `...` 运算符。这个操作符其实有两种对称的形式：*展开*和*剩余*（或*聚集*，我更喜欢）。*展开*形式是一个迭代器消费者。

要*展开*一个迭代器，你必须要有*东西*来把它展开。在 JS 中，有两种可能：一个数组或一个函数调用的参数列表。

一个数组的展开：

```js
// 将迭代器展开到一个数组中，
// 每个迭代值占据一个数组元素的位置。
var vals = [ ...it ];
```

一个函数调用的展开：

```js
// 将迭代器展开到一个函数中，调用时，
// 每个迭代值占据一个参数位置。
doSomethingUseful( ...it );
```

在这两种情况下，迭代器展开形式 `...` 遵循迭代器协议（与 `for..of` 循环相同），从迭代器中获取所有可用的值，并将其放置（也就是展开）到接收上下文（数组，参数列表）中。

### 可迭代对象

从技术上讲，迭代器协议是为使用*可迭代对象*而定义的；可迭代对象是一个可以被迭代的值。

该协议自动从一个可迭代对象创建一个迭代器实例，并且只使用*这个迭代器实例*，直到它完成。这意味着一个单一的可迭代对象可以被使用不止一次；每次都会创建并使用一个新的迭代器实例。

那么我们在哪里可以找到可迭代对象呢？

ES6 将 JS 中的基本数据结构/集合类型定义为可迭代对象。这包括字符串、数组、映射、集合等。

考虑：

```js
// 数组是一个可迭代对象
var arr = [ 10, 20, 30 ];

for (let val of arr) {
    console.log(`Array value: ${ val }`);
}
// Array value: 10
// Array value: 20
// Array value: 30
```

由于数组是可迭代对象，所以我们可以通过 `...` 展开操作符使用迭代器来浅复制一个数组：

```js
var arrCopy = [ ...arr ];
```

我们也可以一次迭代一个字符串中的字符：

```js
var greeting = "Hello world!";
var chars = [ ...greeting ];

chars;
// [ "H", "e", "l", "l", "o", " ",
//   "w", "o", "r", "l", "d", "!" ]
```

`Map` 数据结构使用对象作为键，将一个值（任何类型）与该对象联系起来。Map 的默认迭代与前面看到的不同，因为迭代不仅仅是迭代 Map 的值，而是迭代其*条目*。一个*条目*是一个元组（2个元素的数组），包括一个键和一个值。

考虑：

```js
// 两个 DOM 元素, `btn1` 和 `btn2`

var buttonNames = new Map();
buttonNames.set(btn1,"Button 1");
buttonNames.set(btn2,"Button 2");

for (let [btn,btnName] of buttonNames) {
    btn.addEventListener("click",function onClick(){
        console.log(`Clicked ${ btnName }`);
    });
}
```

在 Map 默认的 `for..of` 迭代循环中，我们使用 `[btn,btnName]` 语法（称为 “数组解构”）将每个元组分解成各自的键/值对（`btn1` / `"Button 1"` 和 `btn2` / `"Button 2"`）。

JS 中内置的每一个可迭代对象都暴露了一个默认迭代，这个迭代很可能符合你的直觉。但如果有必要，你也可以选择一个更具体的迭代。例如，如果我们想只使用上面 `buttonNames` 的值，我们可以调用 `values()` 来得到一个只有值的迭代器：

```js
for (let btnName of buttonNames.values()) {
    console.log(btnName);
}
// Button 1
// Button 2
```

或者如果我们想在数组的迭代中得到索引*和*值，我们可以用 `entries()` 方法做一个条目迭代器：

```js
var arr = [ 10, 20, 30 ];

for (let [idx,val] of arr.entries()) {
    console.log(`[${ idx }]: ${ val }`);
}
// [0]: 10
// [1]: 20
// [2]: 30
```

在大多数情况下，JS 中所有内置的可迭代对象都有三种迭代器形式可供选择：仅键（`keys()`）、仅值（`values()`）和条目（`entries()`）。

除了使用内置的可迭代对象之外，你还可以确保你自己的数据结构遵守迭代协议；这样做意味着你选择允许 `for...of` 循环和 `...` 操作符来使用你的数据。在这个协议上的 “标准化” 意味着代码整体上更容易识别和阅读。

| 注意： |
| :--- |
| 你可能已经注意到这次讨论中发生了细微的变化。我们一开始讨论的是使用**迭代器**，但后来又转到讨论迭代**可迭代对象**。迭代协议期望的是一个*可迭代对象*，但我们也可以直接提供一个*迭代器*的原因是，迭代器也是它本身的一个可迭代对象！当从现有的迭代器创建迭代器实例时，迭代器本身会被返回。|

## 闭包

也许在没有意识到的情况下，几乎每个 JS 开发者都使用过闭包。事实上，闭包是大多数语言中最普遍的编程功能之一。它甚至可能和变量或循环一样重要，这就是它的基础程度。

但它却让人感觉有点隐秘甚至神奇。而且它经常被以非常抽象或非正式的术语来谈论，这对我们确定它到底是什么没有帮助。

我们需要能够认识到程序中哪里用到了闭包，因为闭包的存在或缺乏有时是导致 bug 的原因（甚至是性能问题的原因）。

因此，让我们用一种实用而具体的方式来定义闭包：

> 闭包是指一个函数记住并继续访问其作用域外的变量，即使该函数在不同的作用域中执行。

我们在这里看到两个定义特征。首先，闭包是函数性质的一部分。对象不会得到闭包，函数会。第二，要观察一个闭包，你必须在一个不同于该函数最初定义的作用域中执行它。

考虑：

```js
function greeting(msg) {
    return function who(name) {
        console.log(`${ msg }, ${ name }!`);
    };
}

var hello = greeting("Hello");
var howdy = greeting("Howdy");

hello("Kyle");
// Hello, Kyle!

hello("Sarah");
// Hello, Sarah!

howdy("Grant");
// Howdy, Grant!
```

首先，执行 `greeting(..)` 外部函数，创建一个内部函数 `who(..)` 的实例；该函数封闭了 `msg` 变量，后者是 `greeting(..)` 外部作用域的参数。当内部函数被返回时，它的引用会被赋值给外部作用域中的 `hello` 变量。然后我们第二次调用 `greeting(..)`，创建一个新的内部函数实例，在新的 `msg` 上有一个新的闭包，并返回该引用赋值给 `howdy`。

当 `greeting(..)` 函数完成运行时，通常我们希望它的所有变量都被垃圾回收（从内存中移除）。我们会期望每个 `msg` 都会消失，但它们没有。原因是闭包。由于内部函数实例仍然活着（分别赋值给 `hello` 和 `howdy`），它们的闭包仍然保存着 `msg` 变量。

这些闭包并不是 `msg` 变量值的快照，而是对变量本身的直接连接和保存。这意味着闭包实际上可以随着时间的推移观察（或进行！）这些变量的更新。

```js
function counter(step = 1) {
    var count = 0;
    return function increaseCount(){
        count = count + step;
        return count;
    };
}

var incBy1 = counter(1);
var incBy3 = counter(3);

incBy1();       // 1
incBy1();       // 2

incBy3();       // 3
incBy3();       // 6
incBy3();       // 9
```

内部 `increaseCount()` 函数的每个实例都封闭了来自其外部 `counter(..)` 函数作用域的 `count` 和 `step` 变量。`step` 随着时间的推移保持不变，但 `count` 在每次调用该内部函数时都会更新。由于闭包是包含变量的，而不仅仅是其值的快照，所以这些更新被保留了下来。

闭包在处理异步代码时是最常见的，比如使用回调。考虑：

```js
function getSomeData(url) {
    ajax(url,function onResponse(resp){
        console.log(
            `Response (from ${ url }): ${ resp }`
        );
    });
}

getSomeData("https://some.url/wherever");
// Response (from https://some.url/wherever): ...
```

内部函数 `onResponse(..)` 封闭了 `url`，因此保留并记住它，直到 Ajax 调用返回并执行 `onResponse(..)`。即使 `getSomeData(..)` 立即完成，参数变量 `url` 也会在闭包中持续存在，如果需要的话。

外部作用域不一定是函数 —— 通常是，但并不总是 —— 只是在外部作用域中至少有一个变量可以从内部函数访问：

```js
for (let [idx,btn] of buttons.entries()) {
    btn.addEventListener("click",function onClick(){
       console.log(`Clicked on button (${ idx })!`);
    });
}
```

因为这个循环使用 `let` 声明，所以每次迭代都会得到新的块作用域中的（也就是本地的）`idx` 和 `btn` 变量；这个循环每次都会创建一个新的内部 `onClick(..)` 函数。这个内部函数封闭了 `idx`，只要 `btn` 上设置了点击处理程序，它就会被保留。所以当每个按钮被点击时，它的处理程序可以打印出它的相关索引值，因为处理程序会记住它各自的 `idx` 变量。

记住：这个闭包不是把变量的值封闭（如 `1` 或 `3`），而是封闭变量 `idx` 本身。

闭包是任何语言中最普遍、最重要的编程模式之一。但对 JS 来说尤其如此；很难想象不以某种方式利用闭包就能做任何有用的事情。

如果你仍然对闭包感到不清楚或动摇，第二册 *《作用域和闭包》* 的大部分内容都集中在这个主题上。

## `this` 关键字

JS 最强大的机制之一也是最容易被误解的机制之一：`this` 关键字。一个常见的误解是，一个函数的 `this` 指的是函数本身。由于 `this` 在其他语言中的工作方式，另一个误解是 `this` 指向一个方法所属的实例。这两种说法都是不正确的。

正如前面所讨论的，当一个函数被定义时，它是通过闭包*连接*到它的外层作用域的。作用域是一组规则，用来控制如何解析对变量的引用。

但是函数除了其作用域之外，还有另一个特性影响着它们可以访问的内容。这个特性可以描述为*执行上下文*，它通过 `this` 关键字暴露给函数。

作用域是静态的，在你定义函数的位置，包含了一组固定的变量，但是函数的执行*上下文*是动态的，完全取决于**它如何被调用**（不管它是在哪里定义的，甚至在哪里调用的）。

`this` 不是基于函数定义的固定特性，而是每次函数被调用时确定的动态特性。

关于*执行上下文*的一种看法是，它是一个有形的对象，它的属性在函数执行时被函数所利用。对比一下作用域，作用域也可以被认为是一个*对象*；只不过，*作用域对象*是隐藏在 JS 引擎内部的，它对于那个函数来说总是一样的，它的*属性*是函数内部的可用标识符变量。

```js
function classroom(teacher) {
    return function study() {
        console.log(
            `${ teacher } says to study ${ this.topic }`
        );
    };
}
var assignment = classroom("Kyle");
```

外层的 `classroom(..)` 函数没有引用 `this` 关键字，所以它就像我们目前看到的其他函数一样。但是内部的 `study()` 函数确实引用了 `this`，这使得它成为一个感知到 `this` 的函数。换句话说，它是一个依赖于其*执行上下文*的函数。

| 注意： |
| :--- |
| `study()` 也从它的外部作用域封闭了 `teacher` 变量。 |

`classroom("Kyle")` 返回的内部 `study()` 函数被赋值给一个名为 `assignment` 的变量。那么，如何调用 `assignment()`（又名 `study()`）呢？

```js
assignment();
// Kyle says to study undefined  -- Oops :(
```

在这个片段中，我们把 `assignment()` 作为一个普通的、正常的函数来调用，而没有向它提供任何*执行上下文*。

由于这个程序不是严格模式（见第 1 章 “严格地说”），所以在**没有指定任何上下文**的情况下调用感知上下文的函数会将上下文默认为全局对象（浏览器中的 `window`）。由于没有名为 `topic` 的全局变量（因此在全局对象上也没有这个属性），`this.topic` 解析为 `undefined`。

现在考虑：

```js
var homework = {
    topic: "JS",
    assignment: assignment
};

homework.assignment();
// Kyle says to study JS
```

`assignment` 函数引用的副本被设置为 `homework` 对象上的一个属性，然后被调用 `homework.assignment()`。也就是说，该函数调用的 `this` 将是 `homework` 对象。因此，`this.topic` 解析为 `"JS"`。

最后：

```js
var otherHomework = {
    topic: "Math"
};

assignment.call(otherHomework);
// Kyle says to study Math
```

第三种调用函数的方法是使用 `call(..)` 方法，该方法接收一个对象（这里是 `otherHomework`）来设置函数调用的 `this` 引用。`this.topic` 属性引用解析为 `"Math"`。

同样的感知上下文的函数，当被三种不同的方式调用，根据 `this` 将引用什么对象，每次都会给出不同的答案。

感知上下文的函数，以及它们的动态上下文的好处是，能够更灵活地重复使用一个函数，处理来自不同对象的数据。一个封闭了作用域的函数永远不能引用不同的作用域或变量集。但是一个具有动态 `this` 的感知上下文的函数对于某些任务是相当有帮助的。

## 原型

`this` 是函数执行的特性，而原型是对象的特性，特别是对属性访问的解析。

把原型看成是两个对象之间的链接，这种链接是隐藏在幕后的，虽然有办法暴露和观察它。当一个对象被创建时，就会发生这种原型链接；它与另一个已经存在的对象连接在一起。

通过原型连接在一起的一系列对象被称为 “原型链”。

这种原型链接（即从一个对象 B 到另一个对象 A）的目的是为了使针对 B 的属性/方法的访问，如果 B 没有，则*委托*给 A 处理。属性/方法访问的委托允许两个（或多个！）对象相互合作执行任务。

考虑将一个对象定义为一个普通的字面量：

```js
var homework = {
    topic: "JS"
};
```

`homework` 对象只有一个属性：`topic`。但是，它的默认原型连接到 `Object.prototype` 对象上，而 `Object.prototype` 对象上有常见的内置方法，如 `toString()` 和 `valueOf()` 等。

我们可以观察到这种从 `homework` 到 `Object.prototype` 的原型链接*委托*：

```js
homework.toString();    // [object Object]
```

即使 `homework` 没有定义 `toString()` 方法，`homework.toString()` 也能工作；委托调用了 `Object.prototype.toString()` 以代替它。

### 对象链接

要定义一个对象原型链接，可以使用 `Object.create(..)` 实用程序创建对象:

```js
var homework = {
    topic: "JS"
};

var otherHomework = Object.create(homework);

otherHomework.topic;   // "JS"
```

`Object.create(..)` 的第一个参数指定一个对象来连接新创建的对象，然后返回新创建的（并连接的！）对象。

图 4 显示了三个对象（`otherHomework`、`homework` 和 `Object.prototype`）是如何在原型链中连接的：

<figure>
    <img src="images/fig4.png" width="200" alt="Prototype chain with 3 objects" align="center">
    <figcaption><em>图 4：原型链中的对象</em></figcaption>
    <br><br>
</figure>

通过原型链的委托只适用于查找属性值。如果你对一个对象的属性进行赋值，那将直接应用于该对象，而不管该对象的原型连接到哪里。

| 提示： |
| :--- |
| `Object.create(null)` 创建一个没有原型连接的对象，所以它只是一个独立的对象；在某些情况下，这可能是比较好的。 |

考虑：

```js
homework.topic;
// "JS"

otherHomework.topic;
// "JS"

otherHomework.topic = "Math";
otherHomework.topic;
// "Math"

homework.topic;
// "JS" -- 不是 "Math"
```

对 `topic` 的赋值直接在 `otherHomework` 上创建了一个该名称的属性；对 `homework` 上的 `topic` 属性没有影响。接下来的语句会访问 `otherHomework.topic`，然后我们看到这个新属性的非委托的结果：`"Math"`。

图 5 显示了通过赋值创建 `otherHomework.topic` 属性后的对象/属性：

<figure>
    <img src="images/fig5.png" width="200" alt="3 objects linked, with shadowed property" align="center">
    <figcaption><em>图 5：被遮蔽的 “topic” 属性</em></figcaption>
    <br><br>
</figure>

`otherHomework` 上的 `topic` 在 “遮蔽” 链中的 `homework` 对象上的同名属性。

| 注意： |
| :--- |
| 坦率地说，另一种更为复杂但可能仍然更为常见的创建具有原型连接的对象的方法是使用 “原型类” 模式，`class` 是在 ES6 中添加的（见第 2 章，“类”）。我们将在附录 A “原型类” 中更详细地介绍这个话题。 |

### 重温 `this`

我们在前面介绍了 `this` 关键字，但当考虑到它如何支持原型委托的函数调时，它的真正重要性就显现出来了。事实上，`this` 支持基于函数调用的动态上下文的主要原因之一，是为了使通过原型链委托的对象上的方法调用仍然保持预期的 `this`。

考虑：

```js
var homework = {
    study() {
        console.log(`Please study ${ this.topic }`);
    }
};

var jsHomework = Object.create(homework);
jsHomework.topic = "JS";
jsHomework.study();
// Please study JS

var mathHomework = Object.create(homework);
mathHomework.topic = "Math";
mathHomework.study();
// Please study Math
```

两个对象 `jsHomework` 和 `mathHomework` 各自的原型连接到 `homework` 对象，该对象有 `study()` 函数。`jsHomework` 和 `mathHomework` 都有自己的 `topic` 属性（见图 6）。

<figure>
    <img src="images/fig6.png" width="495" alt="4 objects prototype linked" align="center">
    <figcaption><em>图 6：两个对象连接到一个共同的父对象</em></figcaption>
    <br><br>
</figure>

`jsHomework.study()` 委托给 `homework.study()`，但其执行的 `this`（`this.topic`）因为函数的调用方式而解析为 `jsHomework`，所以 `this.topic` 为 `"JS"`。同理对于 `mathHomework.study()` 委托给 `homework.study()`，但仍然将 `this` 解析为 `mathHomework`，因此 `this.topic` 为 `"Math"`。

如果将 `this` 解析为 `homework`，前面的代码片段的用处就会少得多。然而，在许多其他语言中，似乎 `this` 会是 `homework`，因为 `study()` 方法确实是定义在 `homework` 上的。

与许多其他语言不同，JS 的 `this` 是动态的，这是让原型委托，乃至 `class` 能够如期工作的一个关键要素！

## 问 “为什么？”

本章的预期收获是，JS 表象下有很多东西，而不是表面上看起来很明显的那样。

当你*开始*学习和更深入地了解 JS 的时候，你可以练习和加强的最重要的技能之一就是好奇心，以及当你遇到语言中的某些东西时去问 “为什么？” 的艺术。

尽管本章已经对一些话题进行了相当深入的讲解，但很多细节还是完全略过了。这里需要学习的东西还有很多，而通往这些东西的道路是从你对你的代码提出*正确的*问题开始的。提出正确的问题是成为一个更好的开发人员的关键技能。

在本书的最后一章，我们将简单地看一下 JS 是如何被划分的，这将在 *《你不知道的 JavaScript》* 系列的其他书籍中讲解。另外，不要跳过本书的附录 B，里面有一些练习代码，可以复习本书所涉及的一些主要内容。
