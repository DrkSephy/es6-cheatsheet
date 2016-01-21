# es6-cheatsheet

> A cheatsheet containing ES6 tips, tricks and code snippet examples for your day to day workflow. Contributions are welcome!

## var versus let / const

> Besides var, we now have access to two new identifiers for storing values - **let** and **const**. Unlike **var**, **let** and **const** statements are not hoisted to the top of their enclosing scope. 

An example of using var:

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

However, observe what happens when we replace **var** using **let**:

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

This bug highlights that we need to be careful when refactoring legacy code which uses **var**. 

> **Best Practice**: Leave **var** declarations inside of legacy code to denote that it needs to be carefully refactored. When working on a new codebase, use **let** for variables that will change their value over time, and **const** for variables that will be immutable over time.

## Replacing IIFEs with Blocks

> A common use of **Immediately Invoked Function Expressions** is to enclose values within its scope. In ES6, we now have the ability to create block-based scopes and therefore are not limited purely to function-based scope.

```javascript
(function () {  
    var food = 'Meow Mix';
}());  
console.log(food); // Reference Error
```

Using ES6 Blocks:

```javascript
{  
    let food = 'Meow Mix';
} 
console.log(food); // Reference Error
```

## Arrow Functions

Often times we have nested functions in which we would like to preserve the context of **this** from it's lexical scope. An example is shown below:

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

One common solution to this problem is to store the context of **this** using a variable:

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

Using **Arrow Functions**, the lexical value of **this** isn't shadowed and we can re-write the above as shown:

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    return arr.map((character) => this.name + character); 
}
```

> **Best Practice**: Use **Arrow Functions** whenever you need to preserve the lexical value of **this**. 

Arrow Functions are also more concise when used in function expressions which simply return a value:

```javascript
var squares = arr.map(function (x) { return x * x }); // Function Expression
```

```javascript
const arr = [1, 2, 3, 4, 5];
const squares = arr.map(x => x * x); // Arrow Function for terser implementation
```

> **Best Practice**: Use **Arrow Functions** in place of function expressions when possible.

## Strings

With ES6, the standard library has grown immensely. Along with these changes are new methods which can be used on strings, such as **.includes()** and **.repeat()**.

### .includes( )

```javascript
var string = 'food';
var substring = 'foo';
console.log(string.indexOf(substring) > -1);
```

Instead of checking for a return value `> -1` to denote string containment, we can simply use **.includes()** which will return a boolean:

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

In ES6, we now have access to a terser implementation:

```javascript
// String.repeat(numberOfRepetitions)
'meow'.repeat(3); // 'meowmeowmeow'
```

### Template Literals

Using **Template Literals**, we can now construct strings that have special characters in them without needing to escape them explicitly.

```javascript
var text = "This string contains \"double quotes\" which are escaped."
```

```javascript
let text = `This string contains "double quotes" which are escaped.`
```

**Template Literals** also support interpolation, which makes the task of concatenating strings and values:

```javascript
var name = 'Tiger';
var age = 13;
console.log('My cat is named ' + name + ' and is ' + age + ' years old.');
```

Much simpler:

```javascript
const name = 'Tiger';
const age = 13;
console.log(`My cat is named ${name} and is ${age} years old.`);
```

In ES5, we handled new lines as follows:

```javascript
var text = (
  'cat\n' +
  'dog\n' +
  'nickelodeon'
)
```

Or:

```javascript
var text = [
  'cat',
  'dog',
  'nickelodeon'
].join('\n')
```

**Template Literals** will preserve new lines for us without having to explicitly place them in:

```javascript
var text = (
  `cat
  dog
  nickelodeon`
)
```

**Template Literals** can accept expressions, as well:

```javascript
let today = new Date()
let text = `The time and date is ${today.toLocaleString()}`
```

## Destructuring

Destructuring allows us to extract values from arrays and objects (even deeply nested) and store them in variables with a more convient syntax. 

### Destructuring Arrays

```javascript
var arr = [1, 2, 3, 4];
var a = arr[0];
var b = arr[1];
var c = arr[2];
var d = arr[3];
```

```javascript
var [a, b, c, d] = [1, 2, 3, 4];
console.log(a); // 1
console.log(b); // 2
```

### Destructuring Objects

```javascript
var luke = { occupation: 'jedi', father: 'anakin' }
var occupation = luke.occupation; // 'jedi'
var father = luke.father; // 'anakin'
```

```javascript
var luke = { occupation: 'jedi', father: 'anakin' }
var {occupation, father} = luke;
console.log(occupation); // 'jedi'
console.log(father); // 'anakin'
```

## Modules

Prior to ES6, we used libraries such as **Browserify** to create modules on the client-side, and **require** in **Node.js**. With ES6, we can now directly use modules of all types (AMD and CommonJS).

### Exporting in CommonJS

```javascript
module.exports = 1
module.exports = { foo: 'bar' }
module.exports = ['foo', 'bar']
module.exports = function bar () {}
```

### Exporting in ES6

With ES6, we have various flavors of exporting. We can perform **Named Exports**:

```javascript
export var name = 'David';
export var age  = 25;​​
```

As well as **exporting a list** of objects:

```javascript
function sumTwo(a, b) {
    return a + b;
}

function sumThree(a, b) {
    return a + b + c;
}

export { sumTwo, sumThree };
```

We can also export a value simply by using the **export** keyword:

```javascript
export function sumTwo(a, b) {
    return a + b;
}

export function sumThree(a, b) {
    return a + b + c;
}
```

And lastly, we can **export default bindings**:

```javascript
function sumTwo(a, b) {
    return a + b;
}

function sumThree(a, b) {
    return a + b + c;
}

var api = {
    sumTwo  : sumTwo,
    sumThree: sumThree
}

export default api
```

> **Best Practices**: Always use the **export default** method at **the end** of the module. It makes it clear what is being exported, and saves time by having to figure out what name a value was exported as. Moreso, the common practice in CommonJS modules is to export a single value or object. By sticking to this paradigm, we make our code easily readable and allows us to interpolate between CommonJS and ES6 modules.

### Importing in ES6

ES6 provides us with various flavors of importing. We can import an entire file:

```javascript
import `underscore`
```

> It is important to note that simply **importing an entire file will execute all code at the top level of that file**.

We can also use destructuring to import a list of values from a file:

```javascript
import { sumTwo, sumThree } from 'math/addition'
```

Similar to Python, we have named imports:

```javascript
import { 
  sumTwo as addTwoNumbers, 
  sumThree as sumThreeNumbers} from
} from 'math/addition'
```

And lastly, we can **import all the things**:

```javascript
import * as util from 'math/addition'
```

> **Note**: Values that are exported are **bindings**, not references. Therefore, changing the binding of a variable in one module will affect the value within the exported module. Avoid changing the public interface of these exported values.

## Parameters

In ES5, we had varying ways to handle functions which needed **default values**, **indefinite arguments**, and **named parameters**. With ES6, we can accomplish all of this and more using more concise syntax.

### Default Parameters

```javascript
function addTwoNumbers(x, y) {
    x = x || 0;
    y = y || 0;
    return x + y;
}
```

In ES6, we can simply supply default values for parameters in a function:

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

In ES5, we handled an indefinite number of arguments like so:

```javascript
function logArguments() {
    for (var i=0; i < arguments.length; i++) {
        console.log(arguments[i]);
    }
}
```

Using the **rest** operator, we can pass in an indefinite amount of arguments:

```javascript
function logArguments(...args) {
    for (let arg of args) {
        console.log(arg);
    }
}

### Named Parameters

One of the patterns in ES5 to handle named parameters was to use the **options object** pattern, adopted from jQuery.

```javascript
function initializeCanvas(options) {
    var height = options.height || 600;
    var width  = options.width  || 400;
    var lineStroke = options.lineStroke || 'black';
}
```

We can achieve the same functionality using destructuring as a formal parameter to a function:

```javascript
function initializeCanvas(
    { height=600, width=400, lineStroke='black'}) {
        ...
    }
    // Use variables height, width, lineStroke here
```

If we want to make the entire value optional, we can do so by destructuring an empty object:

```javascript
function initializeCanvas(
    { height=600, width=400, lineStroke='black'} = {}) {
        ...
    }
```

### Spread Operator

We can use the spread operator to pass an array of values to be used as parameters to a function:

```javascript
Math.max(...[-1, 100, 9001, -32]) // 9001
```


