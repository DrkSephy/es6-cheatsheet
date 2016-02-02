# es6-cheatsheet

这是一个 ES2015(ES6) 的Cheatsheet，其中包括提示、小技巧、最佳实践和一些代码片段，帮助你
完成日复一日的开发工作。

## Table of Contents

- [var 与 let / const 声明](#var-versus-let--const)
- [代码执行块替换立即执行函数](#replacing-iifes-with-blocks)
- [箭头函数](#arrow-functions)
- [字符串](#strings)
- [解构](#destructuring)
- [模块](#modules)
- [参数](#parameters)
- [类](#classes)
- [Symbols](#symbols)
- [Maps](#maps)
- [WeakMaps](#weakmaps)
- [Promises](#promises)
- [Generators](#generators)
- [Async Await](#async-await)

## var versus let / const

> 除了 `var` 以外，我们现在多了两个新的标识符来声明变量的存储，它们就是 `let` 和 `const`。
不同于 `var` ，`let` 和 `const` 语句不会造成声明提升。

一个 `var` 的例子:

```javascript
var snack = 'Meow Mix';

function getFood(food) {
    if (food) {
        var snack = 'Friskies';
        return snack;
    }
    return snack;
}

getFood(false); // undefined
```

让我们再观察下面语句中，使用 `let` 替换了 `var` 后的表现：

```javascript
let snack = 'Meow Mix';

function getFood(food) {
    if (food) {
        let snack = 'Friskies';
        return snack;
    }
    return snack;
}

getFood(false); // 'Meow Mix'
```

当我们重构使用 `var` 的老代码时，一定要注意这种变化。盲目使用 `let` 替换 `var` 后可能会导致预期意外的结果。

> **注意**：`let` 和 `const` 是块级作用域语句。所以在语句块以外引用这些变量时，会造成引用错误 `ReferenceError`。

```javascript
console.log(x);

let x = 'hi'; // ReferenceError: x is not defined
```

> **最佳实践**: 在重构老代码时，`var` 声明需要格外的注意。在创建一个新项目时，使用 `let` 声明一个变量，使用 `const` 来声明一个不可改变的常量。

<sup>[(回到目录)](#table-of-contents)</sup>

## Replacing IIFEs with Blocks

我们以往创建一个 **立即执行函数** 时，一般是在函数最外层包裹一层括号。
ES6支持块级作用域（更贴近其他语言），我们现在可以通过创建一个代码块（Block）来实现，不必通过创建一个函数来实现，

```javascript
(function () {
    var food = 'Meow Mix';
}());

console.log(food); // Reference Error
```

使用支持块级作用域的ES6的版本：

```javascript
{
    let food = 'Meow Mix';
}

console.log(food); // Reference Error
```

<sup>[(回到目录)](#table-of-contents)</sup>

## Arrow Functions

一些时候，我们在函数嵌套中需要访问上下文中的 `this`。比如下面的例子：

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    return arr.map(function (character) {
        return this.name + character; // Cannot read property 'name' of undefined
    });
};
```

一种通用的方式是把上下文中的 `this` 保存在一个变量里：

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    var that = this; // Store the context of this
    return arr.map(function (character) {
        return that.name + character;
    });
};
```

我们也可以把 `this` 通过属性传进去：

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    return arr.map(function (character) {
        return this.name + character;
    }, this);
};
```

还可以直接使用 `bind`：

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    return arr.map(function (character) {
        return this.name + character;
    }.bind(this));
};
```

使用 **箭头函数**，`this` 的值不用我们再做如上几段代码的特殊处理，直接使用即可。
上面的代码可以重写为下面这样：

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    return arr.map(character => this.name + character);
};
```

> **最佳实践**：使用箭头函数，再也不用考虑 `this` 的问题了。

当我们编写只返回一个表达式值的简单函数时，也可以使用箭头函数，如下：

```javascript
var squares = arr.map(function (x) { return x * x }); // Function Expression
```

```javascript
const arr = [1, 2, 3, 4, 5];
const squares = arr.map(x => x * x); // Arrow Function for terser implementation
```

> **最佳实践**：尽可能地多使用 **箭头函数**。

<sup>[(回到目录)](#table-of-contents)</sup>

## Strings

在ES6中，标准库也被同样增强了，像字符串对象就新增了 `.includes()` 和 `.repeat()` 方法。

### .includes( )

```javascript
var string = 'food';
var substring = 'foo';

console.log(string.indexOf(substring) > -1);
```

现在，我们可以使用 `.inclues()` 方法，替代以往判断内容 `> -1` 的方式。
`.includes()` 方法会极简地返回一个布尔值结果。

```javascript
const string = 'food';
const substring = 'foo';

console.log(string.includes(substring)); // true
```

### .repeat( )

```javascript
function repeat(string, count) {
    var strings = [];
    while(strings.length < count) {
        strings.push(string);
    }
    return strings.join('');
}
```

在ES6中，我们可以使用一个极简的方法来实现重复字符：

```javascript
// String.repeat(numberOfRepetitions)
'meow'.repeat(3); // 'meowmeowmeow'
```

### Template Literals

使用 **字符串模板字面量**，我可以在字符串中直接使用特殊字符，而不用转义。

```javascript
var text = "This string contains \"double quotes\" which are escaped.";
```

```javascript
let text = `This string contains "double quotes" which are escaped.`;
```

**字符串模板字面量** 还支持直接插入变量，可以实现字符串与变量的直接连接输出。

```javascript
var name = 'Tiger';
var age = 13;

console.log('My cat is named ' + name + ' and is ' + age + ' years old.');
```

更简单的版本：

```javascript
const name = 'Tiger';
const age = 13;

console.log(`My cat is named ${name} and is ${age} years old.`);
```

ES5中，我们要这样生成多行文本：

```javascript
var text = (
    'cat\n' +
    'dog\n' +
    'nickelodeon'
);
```

或者：

```javascript
var text = [
    'cat',
    'dog',
    'nickelodeon'
].join('\n');
```

**字符串模板字面量** 让我们不必特别关注多行字符串中的换行转义符号，直接换行即可：

```javascript
let text = ( `cat
dog
nickelodeon`
);
```

**字符串模板字面量** 内部可以使用表达式，像这样：

```javascript
let today = new Date();
let text = `The time and date is ${today.toLocaleString()}`;
```

<sup>[(回到目录)](#table-of-contents)</sup>

## Destructuring

解构让我们可以使用非常便捷的语法，直接将数组或者对象中的值直接分别导出到多个变量中，

### Destructuring Arrays

**解构数组**

```javascript
var arr = [1, 2, 3, 4];
var a = arr[0];
var b = arr[1];
var c = arr[2];
var d = arr[3];
```

```javascript
let [a, b, c, d] = [1, 2, 3, 4];

console.log(a); // 1
console.log(b); // 2
```

### Destructuring Objects

**解构对象**

```javascript
var luke = { occupation: 'jedi', father: 'anakin' };
var occupation = luke.occupation; // 'jedi'
var father = luke.father; // 'anakin'
```

```javascript
let luke = { occupation: 'jedi', father: 'anakin' };
let {occupation, father} = luke;

console.log(occupation); // 'jedi'
console.log(father); // 'anakin'
```

<sup>[(回到目录)](#table-of-contents)</sup>

## Modules

ES6之前，浏览器端的模块化代码，我们使用像[Browserify](http://browserify.org/)这样的库，
在 **Node.js** 中，我们则使用 [require](https://nodejs.org/api/modules.html#modules_module_require_id)。
在ES6中，我们现在可以直接使用AMD 和 CommonJS这些模块了。

### Exporting in CommonJS

```javascript
module.exports = 1;
module.exports = { foo: 'bar' };
module.exports = ['foo', 'bar'];
module.exports = function bar () {};
```

### Exporting in ES6

在ES6中，提供了多种设置模块出口的方式，比如我们要导出一个变量，那么使用 **变量名** ：

```javascript
export let name = 'David';
export let age  = 25;​​
```

还可以为对象 **导出一个列表**：

```javascript
function sumTwo(a, b) {
    return a + b;
}

function sumThree(a, b, c) {
    return a + b + c;
}

export { sumTwo, sumThree };
```

我们也可以使用简单的一个 `export` 关键字来导出一个结果值：

```javascript
export function sumTwo(a, b) {
    return a + b;
}

export function sumThree(a, b, c) {
    return a + b + c;
}
```

最后，我们可以 **导出一个默认出口**：

```javascript
function sumTwo(a, b) {
    return a + b;
}

function sumThree(a, b, c) {
    return a + b + c;
}

let api = {
    sumTwo,
    sumThree
};

export default api;
```

> **最佳实践**：总是在模块的 **最后** 使用 `export default` 方法。
它让模块的出口更清晰明了，节省了阅读整个模块来寻找出口的时间。
更多的是，在大量CommonJS模块中，通用的习惯是设置一个出口值或者出口对象。
最受这个规则，可以让我们的代码更易读，且更方便的联合使用CommonJS和ES6模块。

### Importing in ES6

ES6提供了好几种模块的导入方式。我们可以单独引入一个文件：

```javascript
import 'underscore';
```

> 这里需要注意的是， **整个文件的引入方式会执行该文件内的最上层代码**。

就像Python一样，我们还可以命名引用：

```javascript
import { sumTwo, sumThree } from 'math/addition';
```

我们甚至可以使用 `as` 给这些模块重命名：

```javascript
import {
    sumTwo as addTwoNumbers,
    sumThree as sumThreeNumbers
} from 'math/addition';
```

另外，我们能 **引入所有的东西（原文：import all the things）** （也称为命名空间引入）

```javascript
import * as util from 'math/addition';
```

最后，我们能可以从一个模块的众多值中引入一个列表：

```javascript
import * as additionUtil from 'math/addtion';
const { sumTwo, sumThree } = additionUtil;
```

当我们引用默认对象，我们可以选择其中的函数：

```javascript
import React from 'react';
const { Component, PropTypes } = React;
```

> **注意**：被导出的值是被 **绑定的（原文：bingdings）**，而不是引用。
所以，改变一个模块中的值的话，会影响其他引用本模块的代码，一定要避免此种改动发生。

<sup>[(回到目录)](#table-of-contents)</sup>

## Parameters

在ES5中，许多种方法来处理函数的 **参数默认值（default values）**，**参数数量（indefinite arguments）**，**参数命名（named parameters）**。
ES6中，我们可以使用非常简洁的语法来处理上面提到的集中情况。

### Default Parameters

```javascript
function addTwoNumbers(x, y) {
    x = x || 0;
    y = y || 0;
    return x + y;
}
```

ES6中，我们可以简单为函数参数启用默认值：

```javascript
function addTwoNumbers(x=0, y=0) {
    return x + y;
}
```

```javascript
addTwoNumbers(2, 4); // 6
addTwoNumbers(2); // 2
addTwoNumbers(); // 0
```

### Rest Parameters

ES5中，遇到参数数量不确定时，我们只能如此处理：

```javascript
function logArguments() {
    for (var i=0; i < arguments.length; i++) {
        console.log(arguments[i]);
    }
}
```

使用 **rest** 操作符，我们可以给函数传入一个不确定数量的参数列表：

```javascript
function logArguments(...args) {
    for (let arg of args) {
        console.log(arg);
    }
}
```

### Named Parameters

命名函数
ES5中，当我们要处理多个 **命名参数** 时，通常会传入一个 **选项对象** 的方式，这种方式被jQuery采用。

```javascript
function initializeCanvas(options) {
    var height = options.height || 600;
    var width  = options.width  || 400;
    var lineStroke = options.lineStroke || 'black';
}
```

我们可以利用上面提到的新特性 **结构** ，来完成与上面同样功能的函数：
We can achieve the same functionality using destructuring as a formal parameter
to a function:

```javascript
function initializeCanvas(
    { height=600, width=400, lineStroke='black'}) {
        // ...
    }
    // Use variables height, width, lineStroke here
```

如果我们需要把这个参数变为可选的，那么只要把该参数结构为一个空对象就好了：

```javascript
function initializeCanvas(
    { height=600, width=400, lineStroke='black'} = {}) {
        // ...
    }
```

### Spread Operator

我们可以利用展开操作符（Spread Operator）来把一组数组的值，当作参数传入：

```javascript
Math.max(...[-1, 100, 9001, -32]); // 9001
```

<sup>[(回到目录)](#table-of-contents)</sup>

## Classes

在ES6以前，我们实现一个类的功能的话，需要首先创建一个构造函数，然后扩展这个函数的原型方法，就像这样：

```javascript
function Person(name, age, gender) {
    this.name   = name;
    this.age    = age;
    this.gender = gender;
}

Person.prototype.incrementAge = function () {
    return this.age += 1;
};
```

继承父类的子类需要这样：

```javascript
function Personal(name, age, gender, occupation, hobby) {
    Person.call(this, name, age, gender);
    this.occupation = occupation;
    this.hobby = hobby;
}

Personal.prototype = Object.create(Person.prototype);
Personal.prototype.constructor = Personal;
Personal.prototype.incrementAge = function () {
    return Person.prototype.incrementAge.call(this) += 20;
};
```

ES6提供了一些语法糖来实现上面的功能，我们可以直接创建一个类：

```javascript
class Person {
    constructor(name, age, gender) {
        this.name   = name;
        this.age    = age;
        this.gender = gender;
    }

    incrementAge() {
      this.age += 1;
    }
}
```

继承父类的子类只要简单的使用 `extends` 关键字就可以了：

```javascript
class Personal extends Person {
    constructor(name, age, gender, occupation, hobby) {
        super(name, age, gender);
        this.occupation = occupation;
        this.hobby = hobby;
    }

    incrementAge() {
        super.incrementAge();
        this.age += 20;
        console.log(this.age);
    }
}
```

> **最佳实践**：ES6新的类语法把我们从晦涩难懂的实现和原型操作中解救出来，这是个非常适合初学者的功能，而且能让我们写出更干净整洁的代码。

<sup>[(回到目录)](#table-of-contents)</sup>

## Symbols

符号在ES6版本之前就已经存在了，但现在我们拥有一个公共的接口来直接使用它们。
下面的例子是创建了两个永远不会冲突的属性。

```javascript
const key = Symbol();
const keyTwo = Symbol();
const object = {};

object[key] = 'Such magic.';
object[keyTwo] = 'Much Uniqueness';

// Two Symbols will never have the same value
>> key === keyTwo
>> false
```

<sup>[(回到目录)](#table-of-contents)</sup>

## Maps

**Maps** 是一个Javascript中很重要（迫切需要）的数据结构。
在ES6之前，我们创建一个 **hash** 通常是使用一个对象：

```javascript
var map = new Object();
map[key1] = 'value1';
map[key2] = 'value2';
```

但是，这样的代码无法避免函数被特别的属性名覆盖的意外情况：

```javascript
> getOwnProperty({ hasOwnProperty: 'Hah, overwritten'}, 'Pwned');
> TypeError: Property 'hasOwnProperty' is not a function
```

**Maps** 让我们使用 `set`，`get` 和 `search` 操作数据。

```javascript
let map = new Map();
> map.set('name', 'david');
> map.get('name'); // david
> map.has('name'); // true
```

Maps最强大的地方在于我们不必只能使用字符串来做key了，现在可以使用任何类型来当作key，而且key不会被强制类型转换为字符串。

```javascript
let map = new Map([
    ['name', 'david'],
    [true, 'false'],
    [1, 'one'],
    [{}, 'object'],
    [function () {}, 'function']
]);

for (let key of map.keys()) {
    console.log(typeof key);
    // > string, boolean, number, object, function
}
```

> **提示**：当使用 `map.get()` 判断值是否相等时，非基础类型比如一个函数或者对象，将不会正常工作。
有鉴于此，还是建议使用字符串，布尔和数字类型的数据类型。

我们还可以使用 `.entries()` 方法来遍历整个map对象：

```javascript
for (let [key, value] of map.entries()) {
    console.log(key, value);
}
```

<sup>[(回到目录)](#table-of-contents)</sup>

## WeakMaps


In order to store private data in < ES5, we had various ways of doing this.
One such method was using naming conventions:

```javascript
class Person {
    constructor(age) {
        this._age = age;
    }

    _incrementAge() {
        this._age += 1;
    }
}
```

在一个开源项目中，命名规则很难维持得一直很好，这样经常会造成一些困扰。
此时，我们可以选择使用WeakMaps来替代Maps来存储我们的数据：

```javascript
let _age = new WeakMap();
class Person {
    constructor(age) {
        _age.set(this, age);
    }

    incrementAge() {
        let age = _age.get(this) + 1;
        _age.set(this, age);
        if (age > 50) {
            console.log('Midlife crisis');
        }
    }
}
```

使用WeakMaps来保存我们私有数据的理由之一是不会暴露出属性名，就像下面的例子中的 `Reflect.ownKeys()`：

```javascript
> const person = new Person(50);
> person.incrementAge(); // 'Midlife crisis'
> Reflect.ownKeys(person); // []
```

一个使用WeakMaps存储数据更实际的例子，就是有关于一个DOM元素和对该DOM元素（有污染）地操作：

```javascript
let map = new WeakMap();
let el  = document.getElementById('someElement');

// Store a weak reference to the element with a key
map.set(el, 'reference');

// Access the value of the element
let value = map.get(el); // 'reference'

// Remove the reference
el.parentNode.removeChild(el);
el = null;

value = map.get(el); // undefined
```

上面的例子中，一个对象被垃圾回收期给销毁了，WeakMaps会自动的把自己内部所对应的键值对数据同时销毁。

> **提示**：结合这个例子，再考虑下jQuery是如何实现缓存带有引用的DOM元素这个功能的，使用了WeakMaps的话，当被缓存的DOM元素被移除的时，jQuery可以自动释放相应元素的内存。
通常情况下，在涉及DOM元素存储和缓存的情况下，使用WeakMaps是非常适合的。

<sup>[(回到目录)](#table-of-contents)</sup>

## Promises

Promises让我们让我们多缩进难看的代码（回调地狱）：

```javascript
func1(function (value1) {
    func2(value1, function (value2) {
        func3(value2, function (value3) {
            func4(value3, function (value4) {
                func5(value4, function (value5) {
                    // Do something with value 5
                });
            });
        });
    });
});
```

写成这样：

```javascript
func1(value1)
    .then(func2)
    .then(func3)
    .then(func4)
    .then(func5, value5 => {
        // Do something with value 5
    });
```

在ES6之前，我们使用[bluebird](https://github.com/petkaantonov/bluebird) 或者
[Q](https://github.com/kriskowal/q)。现在我们有了原生版本的 Promises：

```javascript
new Promise((resolve, reject) =>
    reject(new Error('Failed to fulfill Promise')))
        .catch(reason => console.log(reason));
```

这里有两个处理函数，**resolve**（当Promise执行成功完毕时调用的回调函数） 和 **reject** （当Promise执行不接受时调用的回调函数）

> **Promises的好处**：大量嵌套错误回调函数会使代码变得难以阅读理解。
使用了Promises，我们可以让我们代码变得更易读，组织起来更合理。
此外，Promise处理后的值，无论是解决还是拒绝的结果值，都是不可改变的。

下面是一些使用Promises的实际例子：

```javascript
var fetchJSON = function(url) {
    return new Promise((resolve, reject) => {
        $.getJSON(url)
            .done((json) => resolve(json))
            .fail((xhr, status, err) => reject(status + err.message));
    });
};
```

我们还可以使用 `Promise.all()` 来异步的 **并行** 处理一个数组的数据。

```javascript
var urls = [
    'http://www.api.com/items/1234',
    'http://www.api.com/items/4567'
];

var urlPromises = urls.map(fetchJSON);

Promise.all(urlPromises)
    .then(function (results) {
        results.forEach(function (data) {
        });
    })
    .catch(function (err) {
        console.log('Failed: ', err);
    });
```

<sup>[(回到目录)](#table-of-contents)</sup>

## Generators

Similar to how [Promises](https://github.com/DrkSephy/es6-cheatsheet#promises) allow us to avoid
[callback hell](http://callbackhell.com/), Generators allow us to flatten our code - giving our
asynchronous code a synchronous feel. Generators are essentially functions which we can
[pause their excution](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/yield)
and subsequently return the value of an expression.

A simple example of using generators is shown below:

```javascript
function* sillyGenerator() {
    yield 1;
    yield 2;
    yield 3;
    yield 4;
}

var generator = sillyGenerator();
var value = generator.next();
> console.log(value); // { value: 1, done: false }
> console.log(value); // { value: 2, done: false }
> console.log(value); // { value: 3, done: false }
> console.log(value); // { value: 4, done: false }
```

Where [next](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator/next)
will allow us to push our generator forward and evaluate a new expression. While the above example is extremely
contrived, we can utilize Generators to write asynchronous code in a synchronous manner:

```javascript
// Hiding asynchronousity with Generators

function request(url) {
    getJSON(url, function(response) {
        generator.next(response);
    });
}
```

And here we write a generator function that will return our data:

```javascript
function* getData() {
    var entry1 = yield request('http://some_api/item1');
    var data1  = JSON.parse(entry1);
    var entry2 = yield request('http://some_api/item2');
    var data2  = JSON.parse(entry2);
}
```

By the power of `yield`, we are gauranteed that `entry1` will have the data needed to be parsed and stored
in `data1`.

While generators allow us to write asynchronous code in a synchronous manner, there is no clear
and easy path for error propagation. As such, as we can augment our generator with Promises:

```javascript
function request(url) {
    return new Promise((resolve, reject) => {
        getJSON(url, resolve);
    });
}
```

And we write a function which will step through our generator using `next` which in turn will utilize our
`request` method above to yield a Promise:

```javascript
function iterateGenerator(gen) {
    var generator = gen();
    var ret;
    (function iterate(val) {
        ret = generator.next();
        if(!ret.done) {
            ret.value.then(iterate);
        }
    })();
}
```

<sup>[(回到目录)](#table-of-contents)</sup>

By augmenting our Generator with Promises, we have a clear way of propogating errors through the use of our
Promise `.catch` and `reject`. To use our newly augmented Generator, it is as simple as before:

```javascript
iterateGenerator(function* getData() {
    var entry1 = yield request('http://some_api/item1');
    var data1  = JSON.parse(entry1);
    var entry2 = yield request('http://some_api/item2');
    var data2  = JSON.parse(entry2);
});
```

We were able to reuse our implementation to use our Generator as before, which shows their power. While Generators
and Promises allow us to write asynchronous code in a synchronous manner while retaining the ability to propogate
errors in a nice way, we can actually begin to utilize a simpler construction that provides the same benefits:
[async-await](https://github.com/DrkSephy/es6-cheatsheet#async-await).

<sup>[(回到目录)](#table-of-contents)</sup>

## Async Await

While this is actually an upcoming ES2016 feature, `async await` allows us to perform the same thing we accomplished
using Generators and Promises with less effort:

```javascript
var request = require('request');

function getJSON(url) {
  return new Promise(function(resolve, reject) {
    request(url, function(error, response, body) {
      resolve(body);
    });
  });
}

async function main() {
  var data = await getJSON();
  console.log(data); // NOT undefined!
}

main();
```

Under the hood, it performs similarly to Generators. I highly recommend using them over Generators + Promises. A great resource
for getting up and running with ES7 and Babel can be found [here](http://masnun.com/2015/11/11/using-es7-asyncawait-today-with-babel.html).

<sup>[(回到目录)](#table-of-contents)</sup>
