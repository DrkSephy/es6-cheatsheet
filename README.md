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
