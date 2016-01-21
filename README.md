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

