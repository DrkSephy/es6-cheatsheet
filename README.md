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