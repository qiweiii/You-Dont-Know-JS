# 你不知道的 JavaScript：入门 —— 第 2 版
# 第 2 章：调查 JS

学习 JS 的最好方法是开始编写 JS。

要做到这一点，你需要知道这门语言是如何工作的，这就是我们在这里要关注的。即使你以前使用过其他语言编程，也需要慢慢适应 JS，并确保练习每一个知识点。

本章并不是 JS 所有语法的详细参考，它也不打算成为一个完整的 “JS 入门” 书籍。

相反，我们只是调查该语言的一些主要话题。我们的目标是更好地*感受*它，这样我们就可以更有信心地编写程序。当你阅读本书，以及本系列的其余部分时，我们将陆续更详细地重温这些话题。

请不要期望这一章可以快速阅读。它很长，有很多细节需要咀嚼。慢慢来吧。

| 提示： |
| :--- |
| 如果你还在熟悉 JS，我建议你预留充足的时间来学习本章，把每一节的内容都思考和探索一段时间。查看现有的 JS 程序，并将其中的内容与这里介绍的代码和解释（以及意见！）进行比较。有了对 JS *本质*的坚实基础，你会从本书和本系列的其他部分得到更多的东西。 |

## 每个文件都是一个程序

几乎你使用的每一个网站（Web 应用程序）都是由许多不同的 JS 文件（通常以 .js 文件扩展名）组成的。把整个东西（应用）当成一个程序是很诱人的。但 JS 并不这样认为。

在 JS 中，每个单独的文件都是独立的程序。

这一点很重要的原因是它与错误处理相关。由于 JS 将文件视为程序，一个文件可能会出故障（在分析/编译或执行过程中），但这不一定会阻止下一个文件的处理。显然，如果你的应用程序依赖于五个 .js 文件，而其中一个文件失败，那么整个应用程序最多也只能部分运行。重要的是要确保每个文件都能正常工作，同时尽可能优雅地处理其他文件的故障。

你可能会对将单独的 .js 文件视为独立的 JS 程序感到惊讶。从你使用应用程序的角度来看，它肯定像一个大程序。这是因为应用程序的执行允许这些单独的*程序*合作，并作为一整个来程序运行。

| 注意： |
| :--- |
| 许多项目使用构建工具，最终将项目中单独的文件合并成一个文件，交付给网页。当发生这种情况时，JS 会将这个合并后的文件作为一整个程序来处理。 |

将多个独立的 .js 文件作为一个程序的唯一方式是通过 “全局作用域” 共享它们的状态（以及对它们的公共功能的访问）。它们在这个全局作用域命名空间中混合在一起，所以在运行时它们会作为一个整体。

从 ES6 开始，JS 除了典型的独立 JS 程序格式外，还支持一种模块格式。模块也是基于文件的。如果一个文件通过模块加载机制（如 `import` 语句或 `<script type=module>` 标签）加载，它的所有代码都会被视为一个单一的模块。

虽然你通常不会把一个模块 —— 一个有状态并把操作该状态的方法公开暴露的集合 —— 看作是一个独立的程序，但事实上 JS 仍然会把每个模块单独处理。就像 “全局作用域” 允许独立文件在运行时混在一起一样，将一个模块导入到另一个模块中也允许它们之间的运行时相互协作。

无论一个文件（独立或模块）使用哪种代码组织模式（和加载机制），你仍然应该把每个文件看作是独立的（迷你）程序，然后它可能与其他（迷你）程序合作执行你的整体应用程序的功能。

## 值

程序中最基本的信息单元是值。值就是数据。它们是程序维持状态的方式。在 JS 中，值有两种形式：**原始值**和**对象**。

在程序中，值是用*字面量*表示的：

```js
greeting("My name is Kyle.");
```

在这个程序中，值 `"My name is Kyle."` 是一个原始字符串；字符串是有序的字符集合，通常用来表示单词和句子。

我使用双引号 `"` 字符来*定界*（包围、分离、定义）字符串值。但我也可以使用单引号 `'`  字符。选择哪个引号字符完全是风格上的问题。重要的是，为了代码的可读性和可维护性，选择一个并在整个程序中一致使用它。

另一个给字符串字面量定界的选择是使用反单引号 `` ` `` 字符。然而，这种选择不仅仅是风格上的，还有行为上的差异。请考虑：

```js
console.log("My name is ${ firstName }.");
// My name is ${ firstName }.

console.log('My name is ${ firstName }.');
// My name is ${ firstName }.

console.log(`My name is ${ firstName }.`);
// My name is Kyle.
```

假设这个程序已经定义了一个变量 `firstName`，其字符串值为 `"Kyle"`，那么由 `` ` `` 定界的字符串会将变量表达式（用 `${ ... }` 表示）解析为其当前值。这就是所谓的**内插**。

反单引号 `` ` `` 定界的字符串可以在不包含内插表达式的情况下使用，但这样做就违背了使用这种字符串语法的目的。

```js
console.log(
    `Am I confusing you by omitting interpolation?`
);
// Am I confusing you by omitting interpolation?
```

更好的方法是使用 `"` 或 `'`（同样，选择一个并坚持使用！）来处理*不需要*插值的字符串；保留 `` ` `` 仅用于包含内插表达式的字符串。

除了字符串之外，JS 程序还经常包含其他原始字面量值，如布尔值和数值：

```js
while (false) {
    console.log(3.141592);
}
```

`while` 代表一种循环类型，是一种*当*条件为 true 时重复操作的方式。

在这个例子中，循环永远不会运行（也不会打印任何东西），因为我们使用 `false` 布尔值作为循环条件。`true` 则会导致一个循环永远持续下去，所以要小心!

如你所知，数字 `3.141592` 是数学中 PI 的前六位数的近似值。然而，与其嵌入这样一个值，你通常会使用预先定义的 `Math.PI` 值来达到这个目的。数字的另一种是 `bigint`（大整数）原始类型，它用于存储任意大的数。

数字在程序中最常用于计算次数，如循环迭代，以及访问数值位置上的信息（即数组索引）。稍后我们将介绍数组/对象，但作为一个例子，如果有一个名为 `names` 的数组，我们可以这样访问它第二个位置的元素：

```js
console.log(`My name is ${ names[1] }.`);
// My name is Kyle.
```

我们用 `1` 来表示第二个位置上的元素，而不是 `2`，因为和大多数编程语言一样，JS 数组的索引是基于 0 的（`0` 是第一个位置）。

除了字符串、数字和布尔值之外，JS 程序中还有两个*原始*值是 `null` 和 `undefined`。虽然它们之间存在差异（有些是历史性的，有些是当代的），但在大多数情况下，这两个值的作用都是表示一个值*为空*（或不存在）。

许多开发者喜欢将两者一致对待，也就是说，假设这两个值是无法区分的。如果足够小心，这通常是可能的。然而，最安全和最好的做法是只使用 `undefined` 作为单一的空值，尽管 `null` 看起来很有吸引力，因为它需要打的字更短！

```js
while (value != undefined) {
    console.log("Still got something!");
}
```

最后一个需要注意的原始值是符号，它是一个特殊用途的值，表现为一个隐藏的不可猜测的值。符号几乎只作为对象上的特殊键使用：

```js
hitchhikersGuide[ Symbol("meaning of life") ];
// 42
```

在典型的 JS 程序中，你不会经常遇到直接使用符号的情况。它们大多用在底层代码中，如库和框架中。

### 数组和对象

除了原始值，JS 中的另一种类型的值是对象值。

如前所述，数组是一种特殊的对象，它是一个由有序的、有数字索引的数据列表组成的：

```js
var names = [ "Frank", "Kyle", "Peter", "Susan" ];

names.length;
// 4

names[0];
// Frank

names[1];
// Kyle
```

JS 数组可以容纳任何类型的值，无论是原始值还是对象值（包括其他数组）。正如我们将在第三章的最后看到的那样，即使是函数也是可以在数组或对象中保存的值。

| 注意： |
| :--- |
| 函数和数组一样，是一种特殊的对象（也叫做子类型）。我们稍后会详细介绍函数。 |

对象是更笼统的：一个无序的、有键的、由任何不同的值组成的集合。换句话说，你通过一个字符串位置名（也就是 ”键“ 或 ”属性“）来访问元素，而不是像数组那样通过它的数值位置来访问。比如：

```js
var me = {
    first: "Kyle",
    last: "Simpson",
    age: 39,
    specialties: [ "JS", "Table Tennis" ]
};

console.log(`My name is ${ me.first }.`);
```

在这里，`me` 代表一个对象，`first` 代表该对象（值集合）中信息的位置名称。另一个语法选项是通过属性/键访问对象中的信息，使用方括号 `[]`，如 `me["first"]`。

### 值类型的确定

为了区分值，如果是原始类型，`typeof` 操作符可以告诉你它的内置类型，如果不是则为 `"object"`：

```js
typeof 42;                  // "number"
typeof "abc";               // "string"
typeof true;                // "boolean"
typeof undefined;           // "undefined"
typeof null;                // "object" -- oops, bug!
typeof { "a": 1 };          // "object"
typeof [1,2,3];             // "object"
typeof function hello(){};  // "function"
```

| 警告： |
| :--- |
| 遗憾的是，`typeof null` 返回的是 `"object"`，而不是预期的 `"null"`。另外，对于函数，`typeof` 返回特定的 `"function"`，而对于数组，则没有返回预期的 `"array"`。|

从一种值类型转换为另一种值类型，比如从字符串转换为数字，在 JS 中被称为 “强制”。我们将在本章后面详细介绍。

原始值和对象值在被赋值或传递时的行为是不同的。我们将在附录 A “值与引用” 中介绍这些细节。

## 声明和使用变量

要明确一些在上一节中可能并不明显的东西：在 JS 程序中，值可以以字面量的形式出现（就像前面的许多例子所说明的那样），也可以将它们保存在变量中；将变量看作是值的容器。

变量必须经过声明（创建）才能使用。有各种不同的语法形式来声明变量（也就是 “标识符”），每种形式都有不同的隐含行为。

例如，考虑 `var` 语句：

```js
var myName = "Kyle";
var age;
```

`var` 关键字声明一个要在那部分程序中使用的变量，并可选择赋初值。

另一个类似的关键字是 `let`：

```js
let myName = "Kyle";
let age;
```

`let` 关键字与 `var` 有一些不同，最明显的是 `let` 对变量允许的访问比 `var` 更有限。这被称为 “块作用域”，而不是常规或函数作用域。

考虑：

```js
var adult = true;

if (adult) {
    var myName = "Kyle";
    let age = 39;
    console.log("Shhh, this is a secret!");
}

console.log(myName);
// Kyle

console.log(age);
// Error!
```

试图在 `if` 语句之外访问 `age` 会导致错误，因为 `age` 是在 `if` 的块作用域内，而 `myName` 不是。

块作用域对于限制变量声明在程序中的覆盖范围非常有用，这有助于防止它们的名称意外重叠。

但 `var` 仍然是有用的，因为它传达了 “这个变量将被更广泛的作用域（整个函数）看到”。这两种声明形式都可以适合于程序的任何一个特定部分，视情况而定。

| 注意： |
| :--- |
| 通常建议避免使用 `var`，而使用 `let`（或 `const`！），这通常是由于人们对 `var` 的作用域行为的工作方式感到困惑。我认为这是过于限制性的建议，最终是无益的。这是在假设你无法正确地学习和使用一个功能与其他功能的结合。我相信你*可以*而且*应该*学习任何可用的功能，并在适当的地方使用它们！|

第三种声明形式是 `const`。它和 `let` 一样，但有一个额外的限制，即在声明时必须给它一个值，而且以后不能重新给它赋一个不同的值。

考虑：

```js
const myBirthday = true;
let age = 39;

if (myBirthday) {
    age = age + 1;    // OK!
    myBirthday = false;  // Error!
}
```

`myBirthday` 常量不允许被重新赋值。

`const` 声明的变量并非 “不可改变”，只是不能被重新赋值。给对象值使用 `const` 是不明智的，因为即使变量不能被重新赋值，这些值仍然可以被改变。这将导致潜在的混乱，所以我认为避免这样的情况是明智的：

```js
const actors = [
    "Morgan Freeman", "Jennifer Aniston"
];

actors[2] = "Tom Cruise";   // OK :(
actors = [];                // Error!
```

`const` 在语义上的最佳用法是当你有一个简单的原始值，你想给它起一个有用的名字，比如用 `myBirthday` 而不是 `true`。这使得程序更容易阅读。

| 提示： |
| :--- |
| 如果你坚持只对原始值使用 `const`，你就可以避免混淆任何重新赋值（不允许）与内在改变（允许）！这是最安全和最好的 `const` 的使用方式。 |

除了 `var` / `let` / `const` 之外，还有其他的语法形式可以在不同的作用域内声明标识符（变量）。例如：

```js
function hello(myName) {
    console.log(`Hello, ${ myName }.`);
}

hello("Kyle");
// Hello, Kyle.
```

标识符 `hello` 是在外部作用域中创建的，并且它被自动关联，以便引用函数。但参数 `myName` 只在函数内部创建，因此只能在该函数的作用域内访问。`hello` 和 `myName` 的大致行为与 `var` 声明的变量相似。

另一种声明变量的语法是 `catch` 子句：

```js
try {
    someError();
}
catch (err) {
    console.log(err);
}
```

`err` 是一个块作用域变量，只存在于 `catch` 子句中，就像是用 `let` 声明的一样。

## 函数

在编程中，“函数” 一词有多种含义。例如，在函数式编程的世界里，“函数” 有一个精确的数学定义，并意味着一套严格遵守的规则。

在 JS 中，我们应当认为 “函数” 有另一个含义更广泛的术语：“过程”。一个过程是一个可以被调用一次或多次的语句集合，可以提供一些输入，也可以回馈一个或多个输出。

从早期的 JS 来看，函数的定义是这样的：

```js
function awesomeFunction(coolThings) {
    // ..
    return amazingStuff;
}
```

这被称为函数声明，因为它本身是作为一个语句出现的，而不是作为另一个语句中的表达式。标识符 `awesomeFunction` 和函数值之间的关联发生在代码的编译阶段，在代码执行之前。

与函数声明语句不同，函数表达式可以这样定义和赋值：

```js
// let awesomeFunction = ..
// const awesomeFunction = ..
var awesomeFunction = function(coolThings) {
    // ..
    return amazingStuff;
};
```

这个函数是一个表达式，它被赋值给变量 `awesomeFunction`。与函数声明不同的是，函数表达式直到该语句在运行时，才会与其标识符相关联。

极为重要的是，在 JS 中，函数是可以被赋值（如本段代码所示）和传递的值。事实上，JS 函数是对象值类型的一种特殊类型。并不是所有的语言都把函数当作值来处理，但对于一门语言来说，支持函数式编程模式是必不可少的，JS 就是这样。

JS 函数可以接收输入参数：

```js
function greeting(myName) {
    console.log(`Hello, ${ myName }!`);
}

greeting("Kyle");   // Hello, Kyle!
```

在这段代码中，`myName` 被称为参数，它在函数中作为一个局部变量。函数可以被定义接收任何数量的参数，从零到无数个，只要你认为合适。每个参数都会被赋予在你调用的那个位置（这里是 `"Kyle"`）传递进来的参数值。

函数也可以使用 `return` 关键字返回值：

```js
function greeting(myName) {
    return `Hello, ${ myName }!`;
}

var msg = greeting("Kyle");

console.log(msg);   // Hello, Kyle!
```

你只能 `return` 一个值，但如果你有更多的值要返回，你可以把它们打包成一个对象/数组。

由于函数是值，它们可以作为对象的属性：

```js
var whatToSay = {
    greeting() {
        console.log("Hello!");
    },
    question() {
        console.log("What's your name?");
    },
    answer() {
        console.log("My name is Kyle.");
    }
};

whatToSay.greeting();
// Hello!
```

在这段代码中，三个函数（`greeting()`、`question()` 和 `answer()`）的引用被包含在 `whatToSay` 所关联的对象中。每个函数都可以通过访问属性获取函数引用值来调用。请将这种在对象上直接定义函数的风格与本章后面讨论的更复杂的 `class` 语法进行比较。

在 JS 中，`function` 有许多不同的形式。我们在附录 A “这么多的函数形式” 中对这些变化进行挖掘。

## 比较

在程序中做决策时，需要比较值来确定它们的身份和相互之间的关系。JS 有几种机制可以实现值的比较，我们来仔细看看。

### 相等性

JS 程序中最常见的比较问了一个问题：“这个 X 值和那个 Y 值*一样*吗？” 不过对于 JS 来说，“一样” 到底是什么意思呢？

由于人机工程学和历史原因，其含义比明显的*完全相同*的那种匹配要复杂得多。有时，相等性比较的意图是*完全相同*的匹配，但另一些时候，所需的比较范围更广一些，允许*近似*或*可互换*的匹配。换句话说，我们必须意识到**相等性**比较和**等价**比较之间的细微差别。

如果你有花时间去使用和阅读 JS，你肯定见过所谓的 “三等号” `===` 运算符，也被描述为 “严格相等” 运算符。这看起来相当简单，对吧？当然，“严格” 的意思是严格的，比如狭义的、*完全相同*的。

并不完全是这样的。

是的，大多数参与 `===` 相等性比较的值都会符合这种*完全相同*的直觉。考虑一些例子：

```js
3 === 3.0;              // true
"yes" === "yes";        // true
null === null;          // true
false === false;        // true

42 === "42";            // false
"hello" === "Hello";    // false
true === 1;             // false
0 === null;             // false
"" === null;            // false
null === undefined;     // false
```

| 注意： |
| :--- |
| `===` 的相等性比较的另一种描述方式是 “同时检查值和类型”。在我们目前所看的几个例子中，比如 `42 === "42"`，两个值（数字、字符串等）的*类型*似乎确实是区分因素。不过还有更多的问题。JS 中**所有**的值的比较都会考虑被比较的值的类型，而不*仅仅*是 `===` 操作符。具体来说，`===` 在比较中不允许任何类型转换（也就是 “强制”），而其他 JS 比较*允许*强制。 |

但是 `===` 操作符确实有一些细微差别，许多 JS 开发者都忽略了这个事实，这对他们是不利的。在两种特殊值的情况下，`===` 运算符被设计成会*说谎*。`NaN` 和 `-0`。考虑：

```js
NaN === NaN;            // false
0 === -0;               // true
```

在 `NaN` 的情况下，`===` 运算符*说谎*，说 `NaN` 的出现不等于另一个 `NaN`。在 `-0` 的情况下（是的，这是一个真实的、独特的值，你可以在你的程序中有意使用！），`===` 运算符*说谎*，说它等于常规的 `0` 值。

因为关于这种比较的*谎言*可能会很麻烦，所以最好避免使用 `===` 对它们进行比较。对于 `NaN` 的比较，使用 `Number.isNaN(..)` 实用程序，它不会*说谎*。对于 `-0` 的比较，使用 `Object.is(..)` 实用程序，它也不会*说谎*。如果你愿意，`Object.is(..)` 也可以用于不*说谎*的 `NaN` 检查。幽默的说，你可以把 `Object.is(..)` 看成是 “四等号” `====`，真正严格的比较！

这些*谎言*有更深层次的历史和技术原因，但这并不能改变 `===` 其实并不是*严格*意义上的*完全相等的*比较。

当我们考虑对象值（非原始值）的比较时，故事变得更加复杂。考虑：

```js
[ 1, 2, 3 ] === [ 1, 2, 3 ];    // false
{ a: 42 } === { a: 42 }         // false
(x => x * 2) === (x => x * 2)   // false
```

这里发生了什么？

假设相等性检查考虑的是值的*性质*或*内容*，这似乎是合理的；毕竟，`42 === 42` 考虑的是 `42` 实际的值并进行比较。但当涉及到对象时，感知内容的比较一般被称为 “结构相等性”。

对于对象值，JS 并没有把 `===` 定义为*结构相等性*。相反，`===` 对对象值使用的是*标识相等性*。

在 JS 中，所有的对象值都是通过引用来持有的（参见附录 A 中的 “值与引用”），是通过引用拷贝来赋值和传递的，**并且**在我们现在的讨论中，也是通过引用（标识）来比较的。考虑：

```js
var x = [ 1, 2, 3 ];

// 赋值是通过引用的复制，所以 y 引用了与 x 
// *相同的* 数组，而不是它的另一个拷贝。
var y = x;

y === x;              // true
y === [ 1, 2, 3 ];    // false
x === [ 1, 2, 3 ];    // false
```

在这段代码中，`y === x` 为 true，因为两个变量都持有对同一个初始数组的引用。但是 `=== [1,2,3]` 的比较都失败了，因为 `y` 和 `x` 分别与新的*不同的*数组 `[1,2,3]` 在比较。在这种比较中，数组结构和内容并不重要，只有**引用标识**。

JS 不提供对象值结构相等性的比较机制，只提供引用标识比较。要进行结构相等性比较，需要自己实现检查。

但要注意，这比你想象的要复杂得多。例如，你如何确定两个函数引用是否 “结构上相等”？即使是字符串化来比较它们的源代码文本也不会考虑到闭包等问题。JS 并没有提供结构相等性比较，因为要处理所有的边角案例几乎是无法解决的！

### 强制性比较

强制是指一种类型的值被转换为另一种类型的相应表示（无论在什么程度上）。正如我们将在第 4 章中讨论的那样，强制是 JS 语言的一个核心支柱，而不是一些可以合理避免的可选功能。

但是，当强制与比较操作符（如相等）相遇时，不幸的是，混淆和挫折经常出现。

在更广泛的 JS 社区中，没有什么 JS 特性比 `==` 运算符，即通常所说的 “不精确相等性” 运算符更引起人们的愤怒。所有关于 JS 的写作和公开言论中，大部分都谴责这个操作符设计得很差，在 JS 程序中使用时很危险/漏洞百出。就连该语言的创造者 Brendan Eich 本人也感叹它的设计是个大错误。

据我所知，大部分的挫折感来自于一个相当短的令人困惑的边角案例列表，但更深层次的问题是极其普遍的误解，即它在比较时没有考虑其比较值的类型。

`==` 运算符执行相等性比较的方式与 `===` 执行比较的方式类似。事实上，这两个运算符都考虑了被比较的值的类型。如果比较的是相同的值类型，则 `==` 和 `===` **做的事情完全一样，没有任何区别。**

如果被比较的值类型不同，`==` 与 `===` 的不同之处在于，它允许在比较之前进行强制。换句话说，它们都想比较类型相同的值，但 `==` 允许*先*进行类型转换，一旦双方的类型被转换为相同，那么 `==` 就和 `===` 做同样的事情。与其说 `==` 运算符是 “不精确相等性”，不如说是 “强制相等性”。

考虑：

```js
42 == "42";             // true
1 == true;              // true
```

在这两次比较中，值的类型是不同的，所以 `==` 导致非数字值（`"42"` 和 `true`）在进行比较之前被转换为数字（分别为 `42` 和 `1`）。

只要意识到 `==` 的这个性质 —— 它更喜欢原始的数字比较 —— 就能帮助你避免大多数麻烦的边角案例，比如远离 `"" == 0` 或 `0 == false` 这样的陷阱。

你可能会想，“哦，好吧，我将一直避免任何强制的相等性比较（使用 `===` 代替）来避免这些边角案例”！诶，对不起，这并不像你希望的那样。

你很有可能会使用关系比较运算符，比如 `<`、`>`（甚至 `<=` 和 `>=`）。

就像 `==` 一样，如果被比较的类型已经相匹配，这些运算符会表现得像 “严格的” 一样，但如果类型不同，它们会允许先进行强制（一般来说，转换成数字）。

考虑：

```js
var arr = [ "1", "10", "100", "1000" ];
for (let i = 0; i < arr.length && arr[i] < 500; i++) {
    // 将会运行 3 次
}
```

`i < arr.length` 比较是 “安全的”，因为 `i` 和 `arr.length` 总是数字。但 `arr[i] < 500` 会引起强制，因为 `arr[i]` 的值都是字符串。于是这些比较就变成了 `1 < 500`，`10 < 500`，`100 < 500`，`1000 < 500`。由于第四个是 false，所以循环在第三次迭代后停止。

这些关系运算符通常使用数字比较，除非在**两个**值都已经是字符串的情况下；在这种情况下，它们使用字符串的字母顺序（类似字典）比较：

```js
var x = "10";
var y = "9";

x < y;      // true, watch out!
```

除了在比较中永远不使用不匹配的类型之外，没有办法让这些关系运算符避免强制。这也许是一个值得钦佩的目标，但你还是很有可能会遇到类型*可能*不同的情况。

更明智的做法不是避免强制比较，而是接受并学习其来龙去脉。

强制性比较在 JS 中的其他地方也会出现，比如条件（`if` 等），我们将在附录 A “强制性条件比较” 中重新讨论。

## 我们如何在 JS 中组织

在整个 JS 生态系统中，广泛使用了两种组织代码（数据和行为）的模式：类和模块。这些模式并不是相互排斥的；许多程序可以而且确实同时使用了这两种模式。其他的程序会坚持只使用一种模式，甚至两者都不使用！

在某些方面，这些模式是非常不同的。但有趣的是，在其他方面，它们只是一枚硬币的不同面。精通 JS 需要理解这两种模式，以及它们在什么地方合适（和不合适！）。

### 类

“面向对象”、“面向类” 和 “类” 这些术语背后有很多细节和细微差别的，它们的定义并不通用。

我们将在这里使用一个常见的、有点传统的定义，对于那些有 “面向对象” 语言（如 C++ 和 Java）背景的人来说，这个定义可能是最熟悉的。

程序中的类是对自定义数据结构的 “类型” 的定义，它包括数据和对该数据进行操作的行为。类定义了这样一个数据结构如何工作，但类本身并不是具体的值。为了得到一个可以在程序中使用的具体值，一个类必须被*实例化*（用 `new` 关键字）一次或多次。

考虑：

```js
class Page {
    constructor(text) {
        this.text = text;
    }

    print() {
        console.log(this.text);
    }
}

class Notebook {
    constructor() {
        this.pages = [];
    }

    addPage(text) {
        var page = new Page(text);
        this.pages.push(page);
    }

    print() {
        for (let page of this.pages) {
            page.print();
        }
    }
}

var mathNotes = new Notebook();
mathNotes.addPage("Arithmetic: + - * / ...");
mathNotes.addPage("Trigonometry: sin cos tan ...");

mathNotes.print();
// ..
```

在 `Page` 类中，数据是存储在 `this.text` 成员属性中的一串文本。行为是 `print()`，一个将文本转储到控制台的方法。

对于 `Notebook` 类，数据是一个 `Page` 实例的数组。行为是 `addPage(..)`，一个实例化新 `Page` 页并将其添加到列表中的方法，以及 `print()`（打印出笔记本中的所有页面）。

语句 `mathNotes = new Notebook()` 创建 `Notebook` 类的实例，`page = new Page(text)` 是创建 `Page` 类实例的地方。

行为（方法）只能在实例上调用（而不是类本身），如 `mathNotes.addPage(..)` 和 `page.print()`。

`class` 机制允许包装数据（`text` 和 `pages`）与它们的行为（如 `addPage(..)` 和 `print()`）组织在一起。同样的程序可以在没有任何 “类” 定义的情况下建立，但其组织性可能会差很多，更难阅读和理解，更容易出现错误和不合格的维护。

#### 类继承

传统的 “面向类” 设计所固有的另一个方面是 “继承”（和 “多态”），虽然在 JS 中使用得比较少。考虑：

```js
class Publication {
    constructor(title,author,pubDate) {
        this.title = title;
        this.author = author;
        this.pubDate = pubDate;
    }

    print() {
        console.log(`
            Title: ${ this.title }
            By: ${ this.author }
            ${ this.pubDate }
        `);
    }
}
```

这个 “出版物” 类定义了一组任何出版物可能需要的常见行为。

现在让我们考虑一下更具体的出版物类型，比如 `Book` 和 `BlogPost`：

```js
class Book extends Publication {
    constructor(bookDetails) {
        super(
            bookDetails.title,
            bookDetails.author,
            bookDetails.publishedOn
        );
        this.publisher = bookDetails.publisher;
        this.ISBN = bookDetails.ISBN;
    }

    print() {
        super.print();
        console.log(`
            Publisher: ${ this.publisher }
            ISBN: ${ this.ISBN }
        `);
    }
}

class BlogPost extends Publication {
    constructor(title,author,pubDate,URL) {
        super(title,author,pubDate);
        this.URL = URL;
    }

    print() {
        super.print();
        console.log(this.URL);
    }
}
```

`Book` 和 `BlogPost` 都使用 `extends` 子句来*扩展* `Publication` 的一般定义，以包含额外的行为。每个构造函数中的 `super(..)` 调用委托给父类 `Publication` 的构造函数进行初始化工作，然后它们根据各自的出版物类型（也就是 “子类”）做更具体的事情。

现在考虑使用这些子类：

```js
var YDKJS = new Book({
    title: "You Don't Know JS",
    author: "Kyle Simpson",
    publishedOn: "June 2014",
    publisher: "O'Reilly",
    ISBN: "123456-789"
});

YDKJS.print();
// Title: You Don't Know JS
// By: Kyle Simpson
// June 2014
// Publisher: O'Reilly
// ISBN: 123456-789

var forAgainstLet = new BlogPost(
    "For and against let",
    "Kyle Simpson",
    "October 27, 2014",
    "https://davidwalsh.name/for-and-against-let"
);

forAgainstLet.print();
// Title: For and against let
// By: Kyle Simpson
// October 27, 2014
// https://davidwalsh.name/for-and-against-let
```

请注意，两个子类实例都有一个 `print()` 方法，它是对父类 `Publication` 中*继承的* `print()` 方法的覆盖。这些被覆盖的子类 `print()` 方法都会调用 `super.print()` 来调用继承版的 `print()` 方法。

继承的和覆盖的方法都可以有相同的名称并共存，这一点被称为*多态性*。

继承是一个强大的工具，它可以将数据/行为组织在独立的逻辑单元（类）中，但允许子类通过访问/使用其行为和数据与父类合作。

### 模块

模块模式与类模式的目标基本相同，即把数据和行为归纳为逻辑单元。同样和类一样，模块可以 “包含” 或 “访问” 其他模块的数据和行为，以达到合作的目的。

但模块与类有一些重要的区别。最主要的是，语法完全不同。

#### 经典模块

ES6 在原生 JS 语法的基础上增加了模块语法形式，我们稍后再看。但从 JS 的早期开始，模块就是一个重要而常见的模式，即使没有专门的语法，也被无数的 JS 程序所利用。

一个*经典的模块*的关键标志是一个外部函数（至少运行一次），它返回一个模块的 “实例”，并暴露出一个或多个函数，可以对模块实例的内部（隐藏）数据进行操作。

因为这种形式的模块*只是一个函数*，调用它就会产生一个模块的 “实例”，所以这些函数的另一种描述是 “模块工厂”。

考虑一下之前的的 `Publication`、`Book` 和 `BlogPost` 类的经典模块形式：

```js
function Publication(title,author,pubDate) {
    var publicAPI = {
        print() {
            console.log(`
                Title: ${ title }
                By: ${ author }
                ${ pubDate }
            `);
        }
    };

    return publicAPI;
}

function Book(bookDetails) {
    var pub = Publication(
        bookDetails.title,
        bookDetails.author,
        bookDetails.publishedOn
    );

    var publicAPI = {
        print() {
            pub.print();
            console.log(`
                Publisher: ${ bookDetails.publisher }
                ISBN: ${ bookDetails.ISBN }
            `);
        }
    };

    return publicAPI;
}

function BlogPost(title,author,pubDate,URL) {
    var pub = Publication(title,author,pubDate);

    var publicAPI = {
        print() {
            pub.print();
            console.log(URL);
        }
    };

    return publicAPI;
}
```

将这些形式与 `class` 形式进行比较，相似之处多于不同之处。

`class` 形式中在一个对象实例上存储方法和数据，必须用 `this.` 前缀来访问。对于模块，方法和数据是作为作用域内的标识符变量来访问的，没有任何 `this.` 前缀。

`class` 中，实例的 “API” 是隐含在类定义中的 —— 并且所有的数据和方法都是公共的。使用模块工厂函数，你显式地创建并返回一个对象，其中包含任何公开暴露的方法，任何数据或其他未被引用的方法在工厂函数中保持私有。

这种工厂函数形式还有其他的变体，在整个 JS 中相当常见，甚至在 2020 年也是如此；你可能会在不同的 JS 程序中碰到这些形式。AMD（异步模块定义）、UMD（通用模块定义）和 CommonJS（经典的 Node.js 风格模块）。这些变化不大（也不太兼容）。然而，所有这些形式都依赖于相同的基本原则。

还要考虑这些模块工厂函数的用法（也就是 “实例化”）：

```js
var YDKJS = Book({
    title: "You Don't Know JS",
    author: "Kyle Simpson",
    publishedOn: "June 2014",
    publisher: "O'Reilly",
    ISBN: "123456-789"
});

YDKJS.print();
// Title: You Don't Know JS
// By: Kyle Simpson
// June 2014
// Publisher: O'Reilly
// ISBN: 123456-789

var forAgainstLet = BlogPost(
    "For and against let",
    "Kyle Simpson",
    "October 27, 2014",
    "https://davidwalsh.name/for-and-against-let"
);

forAgainstLet.print();
// Title: For and against let
// By: Kyle Simpson
// October 27, 2014
// https://davidwalsh.name/for-and-against-let
```

这里唯一可以观察到的区别是没有使用 `new`，而是像普通函数一样调用模块工厂。

#### ES 模块

ES 模块（ESM）是在 ES6 中引入 JS 语言的，其目的是与刚才介绍的现有*经典模块*的思想和目的基本相同，特别是考虑到 AMD、UMD 和 CommonJS 的重要变化和用例。

但是，实现方法确实有很大的不同。

首先，没有包装函数来*定义*一个模块。包装上下文是一个文件。ESM 总是基于文件，一个文件，一个模块。

其次，你不会显式地与一个模块的 “API” 交互，而是使用 `export` 关键字将一个变量或方法添加到它的公共 API 定义中。如果某个东西在模块中定义了，但没有被 `export`，那么它就会被隐藏起来（就像*经典模块*一样）。

第三，也许是与之前讨论的模式最明显的不同，你不 “实例化” 一个 ES 模块，你只是 `import` 它来使用它的单一实例。ESM 实际上是 “单例”，因为在你的程序中第一次 `import` 时，只创建了一个实例，而所有其他的 `import` 只是接收到一个对同一个实例的引用。如果你的模块需要支持多个实例，你必须在你的 ESM 定义上提供一个*经典的模块风格的*工厂函数来实现这个目的。

在我们的示例中，我们确实假设了多实例化，所以下面这些代码片段将混合 ESM 和*经典模块*。

考虑文件 `publication.js`：

```js
function printDetails(title,author,pubDate) {
    console.log(`
        Title: ${ title }
        By: ${ author }
        ${ pubDate }
    `);
}

export function create(title,author,pubDate) {
    var publicAPI = {
        print() {
            printDetails(title,author,pubDate);
        }
    };

    return publicAPI;
}
```

从另一个ES模块如 `blogpost.js` 导入和使用这个模块：

```js
import { create as createPub } from "publication.js";

function printDetails(pub,URL) {
    pub.print();
    console.log(URL);
}

export function create(title,author,pubDate,URL) {
    var pub = createPub(title,author,pubDate);

    var publicAPI = {
        print() {
            printDetails(pub,URL);
        }
    };

    return publicAPI;
}
```

最后，为了使用这个模块，我们把它导入到另一个 ES 模块中，比如 `main.js`：

```js
import { create as newBlogPost } from "blogpost.js";

var forAgainstLet = newBlogPost(
    "For and against let",
    "Kyle Simpson",
    "October 27, 2014",
    "https://davidwalsh.name/for-and-against-let"
);

forAgainstLet.print();
// Title: For and against let
// By: Kyle Simpson
// October 27, 2014
// https://davidwalsh.name/for-and-against-let
```

| 注意： |
| :--- |
| `import` 语句中的 `as newBlogPost` 子句是可选的；如果省略，一个名为 `create(..)` 的顶层函数将被导入。在这种情况下，为了便于阅读，我将它重命名为 `create(..)`；它更通用的工厂名 `create(..)` 变成 `newBlogPost(..)` 在语义上更能说明其目的。|

如图所示，ES 模块如果需要支持多实例，可以在内部使用*经典模块*。另外，我们也可以从我们的模块中暴露出一个 `class`，而不是 `create(..)` 工厂函数，结果大致相同。不过，既然你这时已经在使用 ESM 了，我建议你坚持使用*经典模块*而不是 `class`。

如果你的模块只需要一个实例，你可以跳过额外的复杂层次：直接 `export` 它的公共方法。

## “兔洞” 越陷越深

正如本章顶部所承诺的那样，我们刚刚浏览了很多 JS 语言的主要部分。你可能感到头晕脑胀，但在经历了这样的大量信息的冲击之后，这完全是正常的!

即使只是对 JS 的 “简要” 调查，我们也覆盖或提示了一大堆细节，你应该仔细思考并确保自己能够接受。我认真的建议：重读这一章，也许要读几遍。

在下一章，我们将更深入地探讨 JS 核心工作原理的一些重要方面。但在你跟着那个兔子洞深入之前，请确保你已经花了足够的时间来充分消化我们刚刚在这里讲的内容。
