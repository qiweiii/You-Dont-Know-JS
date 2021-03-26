# 你不知道的 JavaScript：入门 —— 第 2 版
# 附录 B：练习、练习、练习！

在本附录中，我们将探索一些练习及其参考答案。这些只是为了*让你开始*对书中的概念进行练习。

## 练习比较

让我们来练习值类型和涉及强制的比较（第 4 章，支柱 3）。

`scheduleMeeting(..)` 应该取一个开始时间（24 小时格式的字符串 “hh:mm”）和一个会议持续时间（分钟数）。如果会议完全在工作日内（根据 `dayStart` 和 `dayEnd` 中指定的时间），应返回 `true`；如果会议违反了工作日的界限，应返回 `false`。

```js
const dayStart = "07:30";
const dayEnd = "17:45";

function scheduleMeeting(startTime,durationMinutes) {
    // ..TODO..
}

scheduleMeeting("7:00",15);     // false
scheduleMeeting("07:15",30);    // false
scheduleMeeting("7:30",30);     // true
scheduleMeeting("11:30",60);    // true
scheduleMeeting("17:00",45);    // true
scheduleMeeting("17:30",30);    // false
scheduleMeeting("18:00",15);    // false
```

自己先尝试解决这个问题。考虑相等关系比较运算符的使用，以及强制如何影响这段代码。当你有了可行的代码，请将你的答案与本附录末尾的 “参考答案” 中的代码进行*比较*。

## 练习闭包

现在让我们练习一下闭包（第 4 章，支柱 1）。

`range(..)` 函数的第一个参数是一个数字，代表所需数字范围中的第一个数字。第二个参数也是一个数字，代表所需范围的末尾（包括）。如果省略了第二个参数，则应返回一个接收该参数的函数。

```js
function range(start,end) {
    // ..TODO..
}

range(3,3);    // [3]
range(3,8);    // [3,4,5,6,7,8]
range(3,0);    // []

var start3 = range(3);
var start4 = range(4);

start3(3);     // [3]
start3(8);     // [3,4,5,6,7,8]
start3(0);     // []

start4(6);     // [4,5,6]
```

先尝试自己解决这个问题。

当你有了可行的代码，请将你的答案与本附录末尾的 “参考答案” 中的代码进行*比较*。

## 练习原型

最后，让我们来研究一下 `this` 和通过原型连接的对象（第 4 章，支柱 2）。

定义一个有三个卷轴的老虎机，可以单独 `spin()`（旋转），然后 `display()` （展示）所有卷轴的当前内容。

单个卷轴的基本行为在下面的 `reel` 对象中定义。但是老虎机需要每个的卷轴对象委托给 `reel`，并且每个卷轴都有一个 `position` 属性。

一个卷轴只*知道如何* `display()` 其当前的老虎机符号，但老虎机通常每个卷轴显示三个符号：当前的（`position`），上一个（`position - 1`）和下一个（`position + 1`）。所以显示老虎机最终应该显示一个 3 × 3 网格的老虎机符号。

```js
function randMax(max) {
    return Math.trunc(1E9 * Math.random()) % max;
}

var reel = {
    symbols: [
        "♠", "♥", "♦", "♣", "☺", "★", "☾", "☀"
    ],
    spin() {
        if (this.position == null) {
            this.position = randMax(
                this.symbols.length - 1
            );
        }
        this.position = (
            this.position + 100 + randMax(100)
        ) % this.symbols.length;
    },
    display() {
        if (this.position == null) {
            this.position = randMax(
                this.symbols.length - 1
            );
        }
        return this.symbols[this.position];
    }
};

var slotMachine = {
    reels: [
        // 这台老虎机需要 3 个独立的卷轴
        // 提示：Object.create(..)
    ],
    spin() {
        this.reels.forEach(function spinReel(reel){
            reel.spin();
        });
    },
    display() {
        // TODO
    }
};

slotMachine.spin();
slotMachine.display();
// ☾ | ☀ | ★
// ☀ | ♠ | ☾
// ♠ | ♥ | ☀

slotMachine.spin();
slotMachine.display();
// ♦ | ♠ | ♣
// ♣ | ♥ | ☺
// ☺ | ♦ | ★
```

自己先尝试解决。

提示：

* 使用 `%` 取模运算符来限制 `position`，因为你在卷轴上循环访问符号。

* 使用 `Object.create(..)` 来创建一个对象，并将其原型连接到另一个对象。一旦连接，委托允许对象在方法调用期间共享 `this` 上下文。

* 与其直接修改卷轴对象来显示三个位置中的每一个位置，不如使用另一个临时对象（再次 `Object.create(..)`），用它自己的 `position`，来进行委托。

当你有了可行的代码，请将你的答案与本附录末尾的 “参考答案” 中的代码进行*比较*。

## 参考答案

请记住，这些参考答案只算是：参考。有许多不同的方法来解决这些练习题。将你的方法与你在这里看到的进行比较，并考虑每种方法的利弊。

“比较”（支柱 3）练习的参考答案：

```js
const dayStart = "07:30";
const dayEnd = "17:45";

function scheduleMeeting(startTime,durationMinutes) {
    var [ , meetingStartHour, meetingStartMinutes ] =
        startTime.match(/^(\d{1,2}):(\d{2})$/) || [];

    durationMinutes = Number(durationMinutes);

    if (
        typeof meetingStartHour == "string" &&
        typeof meetingStartMinutes == "string"
    ) {
        let durationHours =
            Math.floor(durationMinutes / 60);
        durationMinutes =
            durationMinutes - (durationHours * 60);
        let meetingEndHour =
            Number(meetingStartHour) + durationHours;
        let meetingEndMinutes =
            Number(meetingStartMinutes) +
            durationMinutes;

        if (meetingEndMinutes >= 60) {
            meetingEndHour = meetingEndHour + 1;
            meetingEndMinutes =
                meetingEndMinutes - 60;
        }

        // 重组成完全合格的时间字符串
        //（以便于比较）
        let meetingStart = `${
            meetingStartHour.padStart(2,"0")
        }:${
            meetingStartMinutes.padStart(2,"0")
        }`;
        let meetingEnd = `${
            String(meetingEndHour).padStart(2,"0")
        }:${
            String(meetingEndMinutes).padStart(2,"0")
        }`;

        // 注意：由于表达式都是字符串，
        // 这里的比较是按字母顺序的，
        // 但这是安全的，因为它们是完全合格的
        // 时间字符串（即 "07:15" < "07:30"）
        return (
            meetingStart >= dayStart &&
            meetingEnd <= dayEnd
        );
    }

    return false;
}

scheduleMeeting("7:00",15);     // false
scheduleMeeting("07:15",30);    // false
scheduleMeeting("7:30",30);     // true
scheduleMeeting("11:30",60);    // true
scheduleMeeting("17:00",45);    // true
scheduleMeeting("17:30",30);    // false
scheduleMeeting("18:00",15);    // false
```

----

“闭包”（支柱 1）练习的参考答案：

```js
function range(start,end) {
    start = Number(start) || 0;

    if (end === undefined) {
        return function getEnd(end) {
            return getRange(start,end);
        };
    }
    else {
        end = Number(end) || 0;
        return getRange(start,end);
    }


    // **********************

    function getRange(start,end) {
        var ret = [];
        for (let i = start; i <= end; i++) {
            ret.push(i);
        }
        return ret;
    }
}

range(3,3);    // [3]
range(3,8);    // [3,4,5,6,7,8]
range(3,0);    // []

var start3 = range(3);
var start4 = range(4);

start3(3);     // [3]
start3(8);     // [3,4,5,6,7,8]
start3(0);     // []

start4(6);     // [4,5,6]
```

----

“原型”（支柱 2）练习的参考答案：

```js
function randMax(max) {
    return Math.trunc(1E9 * Math.random()) % max;
}

var reel = {
    symbols: [
        "♠", "♥", "♦", "♣", "☺", "★", "☾", "☀"
    ],
    spin() {
        if (this.position == null) {
            this.position = randMax(
                this.symbols.length - 1
            );
        }
        this.position = (
            this.position + 100 + randMax(100)
        ) % this.symbols.length;
    },
    display() {
        if (this.position == null) {
            this.position = randMax(
                this.symbols.length - 1
            );
        }
        return this.symbols[this.position];
    }
};

var slotMachine = {
    reels: [
        Object.create(reel),
        Object.create(reel),
        Object.create(reel)
    ],
    spin() {
        this.reels.forEach(function spinReel(reel){
            reel.spin();
        });
    },
    display() {
        var lines = [];

        // 将 3 行全部在老虎机上显示
        for (
            let linePos = -1; linePos <= 1; linePos++
        ) {
            let line = this.reels.map(
                function getSlot(reel){
                    var slot = Object.create(reel);
                    slot.position = (
                        reel.symbols.length +
                        reel.position +
                        linePos
                    ) % reel.symbols.length;
                    return slot.display();
                }
            );
            lines.push(line.join(" | "));
        }

        return lines.join("\n");
    }
};

slotMachine.spin();
slotMachine.display();
// ☾ | ☀ | ★
// ☀ | ♠ | ☾
// ♠ | ♥ | ☀

slotMachine.spin();
slotMachine.display();
// ♦ | ♠ | ♣
// ♣ | ♥ | ☺
// ☺ | ♦ | ★
```

这本书就到此为止。但现在是时候寻找真正的项目来实践这些想法了。请继续编码，因为这是最好的学习方法！
