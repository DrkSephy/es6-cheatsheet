# es6-cheatsheet

ES2015(ES6)의 Tip & Tricks, 좋은 활용사례들과 코드 예제들이 포함된 cheatsheet입니다.
이 문서는 한국어 번역 버전입니다. 오역 제보와 더 좋은 번역을 기다리고 있습니다!

## 목차

- [var VS let / const](#var-vs-let--const)
- [IIFE를 블록으로 교체하기](#iife를-블록으로-교체하기)
- [애로우 펑션](#애로우-펑션)
- [문자열](#문자열)
- [Destructuring](#destructuring)
- [모듈](#모듈)
- [파라미터(Parameter)](#파라미터parameter)
- [클래스](#클래스)
- [심볼(Symbol)](#심볼symbol)
- [맵(Map)](#맵map)
- [위크맵(WeakMap)](#위크맵weakmap)
- [Promise](#promise)
- [제너레이터(Generator)](#제너레이터generator)
- [Async Await](#async-await)
- [Getter/Setter 함수](#getter와-setter-함수)
- [License](#license)

## var VS let / const

> 기존의 `var`에 더해 `let`과 `const`라는 값을 저장하기 위한 두 개의 새로운 식별자가 추가되었습니다. `var`와는 다르게, `let`과 `const` 상태는 스코프 내 최상단으로 호이스팅되지 않습니다.

다음은 `var`를 활용한 예제입니다.

```javascript
var snack = '허니버터칩';

function getFood(food) {
    if (food) {
        var snack = '스윙칩';
        return snack;
    }
    return snack;
}

getFood(false); // undefined
```

그러나, `var` 대신 `let`을 사용하면 다음과 같이 동작합니다.

```javascript
let snack = '허니버터칩';

function getFood(food) {
    if (food) {
        let snack = '스윙칩';
        return snack;
    }
    return snack;
}

getFood(false); // '허니버터칩'
```

이런 변경점으로 인해 `var`를 사용했던 레거시 코드를 리팩토링할 때 더욱 조심해야 합니다. 무턱대고 `var` 대신 `let`을 사용하면 예상치 못한 동작을 할 수도 있습니다.

> **Note**: `let`과 `const`는 블록 스코프 식별자입니다. 따라서, 블록 스코프 식별자로 정의하기 전에 참조하게 되면 `ReferenceError`를 발생시킵니다.

```javascript
console.log(x);

let x = 'hi'; // ReferenceError: x is not defined
```

> **Best Practice**: 더 조심스럽게 리팩토링하기 위해서 레거시 코드 내에 `var`선언을 남겨두세요. 새로운 코드베이스에서 작업하게 될 때, 변수 사용을 위해서 `let`을 사용하고, 상수 사용을 위해서 `const`를 사용하세요.

<sup>[(목차로 돌아가기)](#목차)</sup>

## IIFE를 블록으로 교체하기

> **Immediately Invoked Function Expressions(IIFE)**는 일반적으로 변수들을 별도의 스코프 안에서만 쓰기 위해서 사용되었습니다. ES6에서는 블록을 기반으로 스코프를 만들 수 있게 되었으므로, 더 이상 함수 기반으로 스코프를 만들지 않아도 됩니다.

```javascript
(function () {
    var food = '허니버터칩';
}());

console.log(food); // Reference Error
```

ES6 블록을 사용하는 경우,

```javascript
{
    let food = '허니버터칩';
};

console.log(food); // Reference Error
```

<sup>[(목차로 돌아가기)](#목차)</sup>

## 애로우 펑션

종종 다음과 같이 중첩된 함수 안에서 `this`의 문맥(context)를 보존해야 할 일이 있습니다.

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

보통 이 문제를 해결하기 위해 다음과 같이 별도의 변수를 사용해서 `this`의 문맥을 저장합니다.

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    var that = this; // this의 문맥을 저장합니다.
    return arr.map(function (character) {
        return that.name + character;
    });
};
```

또는, 다음과 같은 방법으로 `this` 문맥을 통과시킬 수도 있습니다.

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

이 뿐만 아니라 문맥을 bind 할 수도 있습니다.

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

**애로우 펑션**을 사용하면, `this`의 문맥 값이 사라지지 않기 때문에 위의 코드는 다음과 같이 다시 쓸 수 있습니다.

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    return arr.map(character => this.name + character);
};
```

> **Best Practice**: `this`의 문맥 값을 보존해야할 때마다 **애로우 펑션**을 사용하세요.

또한 애로우 펑션은 간단한 값을 리턴하는 함수(함수 표현식)가 필요할 때 사용하면 더욱 간결합니다.

```javascript
var squares = arr.map(function (x) { return x * x }); // 함수 표현식
```

```javascript
const arr = [1, 2, 3, 4, 5];
const squares = arr.map(x => x * x); // 간결한 구현을 위한 애로우 펑션
```

> **Best Practice**: 가능하다면 함수 표현식 대신 **애로우 펑션**을 활용하세요.

<sup>[(목차로 돌아가기)](#목차)</sup>

## 문자열

ES6에서는 표준 라이브러리가 크게 확장되었습니다. 이러한 변경에 맞춰 문자열에도 `.includes()`와 `.repeat()` 같은 새로운 메소드가 추가되었습니다.

### .includes( )

```javascript
var string = 'food';
var substring = 'foo';

console.log(string.indexOf(substring) > -1);
```

문자열 포함 여부를 구현하기 위해서 리턴 값이 `-1`보다 큰 지 체크하는 것 대신, 간단하게 불린 값을 리턴하는 `.includes()` 메소드를 사용할 수 있습니다.

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

ES6에서는 이제 간결하게 구현할 수 있습니다.

```javascript
// String.repeat(numberOfRepetitions)
'야옹'.repeat(3); // '야옹야옹야옹'
```

### 템플릿 리터럴

**템플릿 리터럴**을 사용하면 명시적인 문자열 이스케이프를 사용하지 않아도 특수문자를 포함한 문자열을 구축할 수 있습니다.

```javascript
var text = "이 문자열은 이스케이프 된 \"큰 따옴표\"를 포함합니다.";
```

```javascript
let text = `이 문자열은 이스케이프 된 "큰 따옴표"를 포함합니다.`;
```

**템플릿 리터럴**은 문자열과 값을 연결시키는 문자열 Interpolation도 지원합니다.

```javascript
var name = '나비';
var age = 13;

console.log('제 고양이의 이름은 ' + name + '이고, 나이는 ' + age + '살 입니다.');
```

더 간단하게 구현하면,

```javascript
const name = '나비';
const age = 13;

console.log(`제 고양이의 이름은 ${name}이고, 나이는 ${age}살 입니다.`);
```

ES5에서는 개행을 구현하기 위해서 다음과 같이 했습니다.

```javascript
var text = (
    '고양이\n' +
    '강아지\n' +
    '투니버스'
);
```

혹은 이렇게,

```javascript
var text = [
    '고양이',
    '강아지',
    '투니버스'
].join('\n');
```

**템플릿 리터럴**은 명시적으로 표시하지 않아도 개행을 보존합니다.

```javascript
let text = ( `고양이
강아지
투니버스`
);
```

뿐만 아니라, **템플릿 리터럴**은 표현식에도 접근할 수 있습니다.

```javascript
let today = new Date();
let text = `현재 시각은 ${today.toLocaleString()}입니다.`;
```

<sup>[(목차로 돌아가기)](#목차)</sup>

## Destructuring

Destructuring은 배열 혹은 객체(깊게 중첩된 것도 포함하여)에서 편리한 문법을 이용해 값을 추출하고 저장하는데에 활용됩니다.

### 배열 Destructuring

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

### 객체 Destructuring

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

<sup>[(목차로 돌아가기)](#목차)</sup>

## 모듈

ES6 이전엔, 클라이언트 단은 [Browserify](http://browserify.org/), **Node.js**에서는 [require](https://nodejs.org/api/modules.html#modules_module_require_id)같은 라이브러리를 사용했습니다. 이제 ES6에서는 모든 종류(AMD와 CommonJS)의 모듈을 직접적으로 사용할 수 있습니다.

### CommonJS의 모듈 내보내기(Export)

```javascript
module.exports = 1;
module.exports = { foo: 'bar' };
module.exports = ['foo', 'bar'];
module.exports = function bar () {};
```

### ES6의 모듈 내보내기

ES6에서는, 다양한 방식으로 모듈을 내보낼 수 있습니다. 그 중 **지정 내보내기(named export)**방식은 다음과 같습니다.

```javascript
export let name = 'David';
export let age  = 25;​​
```

또한 객체를 이용한 **리스트 내보내기(exporting a list)**방식도 있습니다.

```javascript
function sumTwo(a, b) {
    return a + b;
}

function sumThree(a, b, c) {
    return a + b + c;
}

export { sumTwo, sumThree };
```

간단하게 `export` 키워드만 활용하면 함수, 객체, 값 등을 내보낼 수 있습니다.

```javascript
export function sumTwo(a, b) {
    return a + b;
}

export function sumThree(a, b, c) {
    return a + b + c;
}
```

마지막으로, **디폴트 모듈로 내보내기(default binding export)**도 가능합니다.

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

/* 위 코드는 아래와 같습니다.
 * export { api as default };
 */
```

> **Best Practice**: `export default`메소드는 항상 모듈 코드의 **마지막**에 위치해야 합니다. 그래야 내보내는 것이 무엇인지 분명해지며, 내보내는 값의 이름을 확인하는 시간을 절약할 수 있습니다. 그 이상으로, CommonJS의 일반적인 관행은 단일 값이나 객체를 내보내는 것입니다. 이런 컨벤션을 따름으로서, 코드의 가독성을 좋게 만들 수 있고 CommonJS와 ES6 모듈을 모두 사용할 수 있게 됩니다.


### ES6의 모듈 불러오기(import)

ES6에서는 다양한 방식으로 모듈을 불러올 수 있습니다. 다음과 같이 파일 전체를 불러올 수 있습니다.

```javascript
import 'underscore';
```

> 이렇게 단순히 파일 전체를 불러오면 그 파일의 최상단에서 불러온 모든 코드가 실행된다는 점에 유의하시기 바랍니다.

파이썬하고 유사한 지정 불러오기(named import)를 사용할 수 있습니다.

```javascript
import { sumTwo, sumThree } from 'math/addition';
```

다음과 같이 불러온 모듈의 이름을 새로 작성할 수도 있습니다.

```javascript
import {
    sumTwo as addTwoNumbers,
    sumThree as sumThreeNumbers
} from 'math/addition';
```

거기에 더해서, **모두 불러오기**(네임스페이스 불러오기)도 가능합니다.

```javascript
import * as util from 'math/addition';
```

마지막으로, 모듈에서 값들의 리스트를 불러올 수도 있습니다.

```javascript
import * as additionUtil from 'math/addition';
const { sumTwo, sumThree } = additionUtil;
```

디폴트 모듈은 다음과 같이 불러올 수 있습니다.

```javascript
import api from 'math/addition';
// 위 코드는 이렇게 표현할 수도 있습니다: import { default as api } from 'math/addition';
```

가급적 간단한 형태로 모듈을 내보내는 것이 좋지만, 필요하다면 때때로 디폴트 모듈을 포함해 여러 이름을 섞어서 내보낼 수도 있습니다.

```javascript
// foos.js
export { foo as default, foo1, foo2 };
```

이 모듈은 아래와 같이 불러올 수 있습니다.

```javascript
import foo, { foo1, foo2 } from 'foos';
```

React처럼 CommonJS 문법을 사용해 내보낸 모듈을 불러올 때는 다음과 같이 쓰면 됩니다.

```javascript
import React from 'react';
const { Component, PropTypes } = React;
```

다음과 같이 더욱 간결해질 수도 있습니다.

```javascript
import React, { Component, PropTypes } from 'react';
```

> **Note**: 내보내지는 값은 참조되는 것이 아니라 **바인딩**되는 것입니다. 그러므로, 어떤 모듈의 변수 바인딩을 바꾸게 되면 내보낸 모듈 내에서만 바뀌게 됩니다. 이렇게 내보낸 모듈의 값의 인터페이스를 바꾸는 것은 피하세요.

<sup>[(목차로 돌아가기)](#목차)</sup>

## 파라미터(Parameter)

ES5에서는 **디폴트 값(default values)**이나 **정의되지 않은 인자(indefinite arguments)** 혹은 **네임드 파라미터(named parameters)**를 다루는 함수를 구현하는 방법이 너무 많았습니다. ES6에서는 더욱 간결한 문법을 통해 이것들을 모두 다룰 수 있습니다.

### 디폴트 파라미터(Default Parameter)

```javascript
function addTwoNumbers(x, y) {
    x = x || 0;
    y = y || 0;
    return x + y;
}
```

ES6에서는 함수 내 파라미터의 디폴트 값을 간단하게 설정할 수 있습니다.

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

### 레스트 파라미터(Rest Parameter)

ES5에서는 인수의 숫자가 가변적인 경우 다음과 같이 처리했습니다.

```javascript
function logArguments() {
    for (var i=0; i < arguments.length; i++) {
        console.log(arguments[i]);
    }
}
```

**레스트(rest)**연산자를 사용하면, 다음과 같이 가변적인 숫자의 인수를 넘길 수 있습니다.

```javascript
function logArguments(...args) {
    for (let arg of args) {
        console.log(arg);
    }
}
```

### 네임드 파라미터(Named Parameter)

ES5의 네임드 파라미터를 처리하는 방법 중 하나는 jQuery에서 차용된 **options object** 패턴을 사용하는 것입니다.

```javascript
function initializeCanvas(options) {
    var height = options.height || 600;
    var width  = options.width  || 400;
    var lineStroke = options.lineStroke || 'black';
}
```

파라미터에 destructuring을 사용하면 같은 기능을 구현할 수 있습니다.

```javascript
function initializeCanvas(
    { height=600, width=400, lineStroke='black'}) {
        // 여기에서 height, width, lineStroke 변수를 사용합니다.
    }
```

만약 모든 파라미터를 선택적으로 넘기고 싶다면, 다음과 같이 빈 객체로 destructuring 하면 됩니다.

```javascript
function initializeCanvas(
    { height=600, width=400, lineStroke='black'} = {}) {
        // ...
    }
```

### 전개 연산자(Spread Operator)

ES5에서는 배열 내 숫자들의 최대 값을 찾기 위해서 `Math.max`에 `apply` 메소드를 사용했습니다.

```javascript
Math.max.apply(null, [-1, 100, 9001, -32]); // 9001
```

ES6에서는 이제 전개 연산자를 이용해서 함수에 파라미터로 배열을 넘길 수 있습니다.

```javascript
Math.max(...[-1, 100, 9001, -32]); // 9001
```

다음과 같이 직관적인 문법을 통해 쉽게 배열 리터럴을 합칠 수도 있습니다.

```javascript
let cities = ['서울', '부산'];
let places = ['여수', ...cities, '제주']; // ['여수', '서울', '부산', '제주']
```

<sup>[(목차로 돌아가기)](#목차)</sup>

## 클래스

ES6 이전에는, 생성자 함수(constructor)를 만들고 프로토타입을 확장해서 프로퍼티를 추가하여 클래스를 구현했습니다.

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

그리고 다음과 같이 클래스를 상속했습니다.

```javascript
function Personal(name, age, gender, occupation, hobby) {
    Person.call(this, name, age, gender);
    this.occupation = occupation;
    this.hobby = hobby;
}

Personal.prototype = Object.create(Person.prototype);
Personal.prototype.constructor = Personal;
Personal.prototype.incrementAge = function () {
    Person.prototype.incrementAge.call(this);
    this.age += 20;
    console.log(this.age);
};
```

ES6는 이런 간단한 구현을 위해 편리한 문법을 제공합니다. 이제 클래스를 직접 만들 수 있습니다.

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

그리고 `extends` 키워드를 사용해서 상속할 수 있습니다.

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

> **Best Practice**: 클래스를 만들기 위한 ES6의 문법은 내부적으로 어떻게 프로토타입으로 구현되는지 모호하지만, 초보자들에게 좋은 기능이고 더 깨끗한 코드를 작성하도록 도와줍니다.

<sup>[(목차로 돌아가기)](#목차)</sup>

## 심볼(Symbol)

심볼은 ES6 이전에도 존재했지만, 이제 직접적으로 심볼을 사용할 수 있는 공식 인터페이스가 제공됩니다. 심볼은 고유하고 수정 불가능한 데이터 타입이고 모든 객체의 식별자로 활용할 수 있습니다.

### Symbol()

`Symbol()` 혹은 `Symbol(description)` 메소드를 호출하면 전역적으로 사용할 수 없는 고유한 심볼이 생성될 것입니다. `Symbol()`은 써드 파티 라이브러리의 객체 혹은 네임스페이스에 충돌할 염려가 없는 새로운 코드를 덧입히는데 종종 쓰입니다. 예를 들어, 나중에 라이브러리가 업데이트 되더라도 겹칠 우려가 없이 `React.Component` 클래스에 `refreshComponent` 메소드를 추가하고 싶다면 다음과 같이 할 수 있습니다.

```javascript
const refreshComponent = Symbol();

React.Component.prototype[refreshComponent] = () => {
    // do something
}
```


### Symbol.for(key)

`Symbol.for(key)`는 여전히 고유하고 수정 불가능한 심볼을 생성하지만, 전역적으로 사용 가능합니다. `Symbol.for(key)`를 두 번 호출하면 두 번 다 같은 심볼 인스턴스를 반환합니다. 주의하세요. `Symbol(description)`에서는 그렇지 않습니다.

```javascript
Symbol('foo') === Symbol('foo') // false
Symbol.for('foo') === Symbol('foo') // false
Symbol.for('foo') === Symbol.for('foo') // true
```

일반적으로 심볼, 특히 `Symbol.for(key)`은 상호 운용성을 위해 사용합니다. 상호 운용성은 몇 가지 알려진 인터페이스를 포함하는 서드 파티 라이브러리의 객체에 인자로 심볼 멤버 형태의 코드를 사용함으로서 만족될 수 있습니다. 예를 들면,

```javascript
function reader(obj) {
    const specialRead = Symbol.for('specialRead');
    if (obj[specialRead]) {
        const reader = obj[specialRead]();
        // do something with reader
    } else {
        throw new TypeError('객체를 읽을 수 없습니다.');
    }
}
```

또 다른 라이브러리에선 이렇게 할 수 있습니다.

```javascript
const specialRead = Symbol.for('specialRead');

class SomeReadableType {
    [specialRead]() {
        const reader = createSomeReaderFrom(this);
        return reader;
    }
}
```

> 상호 운용성을 위해 심볼을 사용하는 주목할 만한 예는 모든 반복 가능한(iterable) 타입 혹은 반복자(iterator)에 존재하는 `Symbol.iterator`입니다. 배열, 문자열, 생성자, 등 반복 가능한 타입은 이 메소드를 통해 호출되면 반복자 인터페이스를 포함한 객체 형태로 리턴됩니다.

<sup>[(목차로 돌아가기)](#목차)</sup>

## 맵(Map)
**맵**은 자바스크립트에서 자주 필요한 데이터 구조입니다. ES6 이전엔 객체를 이용해서 **해시** 맵을 생성했습니다.

```javascript
var map = new Object();
map[key1] = 'value1';
map[key2] = 'value2';
```

하지만, 이 방법은 특정 프로퍼티 이름으로 인한 예상치 못한 함수 오버라이드(override)로부터 안전하지 않습니다.

```javascript
> getOwnProperty({ hasOwnProperty: 'Hah, overwritten'}, 'Pwned');
> TypeError: Property 'hasOwnProperty' is not a function
```

실제로 **맵**은 값을 위해 `get`, `set` 그리고 `search` 등의 메소드를 제공합니다.

```javascript
let map = new Map();
> map.set('name', '현섭');
> map.get('name'); // 현섭
> map.has('name'); // true
```

맵의 가장 놀라운 점은 더 이상 키 값으로 문자열만 쓰지 않아도 된다는 것입니다. 이제 키 값으로 어떤 타입을 전달해도 문자열로 형변환되지 않습니다.

```javascript
let map = new Map([
    ['이름', '현섭'],
    [true, 'false'],
    [1, '하나'],
    [{}, '객체'],
    [function () {}, '함수']
]);

for (let key of map.keys()) {
    console.log(typeof key);
    // > string, boolean, number, object, function
}
```

> **Note**: 함수나 객체처럼 기본형 데이터 타입이 아닌 타입을 사용하면 `map.get()`같은 메소드를 사용할 때 비교 연산자가 제대로 동작하지 않습니다. 따라서, 문자열, 불린, 숫자 같은 기본형 데이터 타입을 계속 쓰는 것이 좋습니다.

또한 `.entries()`를 사용하면 맵을 순회할 수 있습니다.

```javascript
for (let [key, value] of map.entries()) {
    console.log(key, value);
}
```

<sup>[(목차로 돌아가기)](#목차)</sup>

## 위크맵(WeakMap)

ES6 이전에는 private 데이터를 저장하기 위해서 많은 방법을 사용했습니다. 그 중 한가지가 네이밍 컨벤션을 이용한 방법이죠.

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

그러나 네이밍 컨벤션은 코드베이스에 대해 혼란을 일으킬 수 있고, 항상 유지된다는 보장을 할 수 없었습니다. 이제 위크맵으로 이런 값을 저장할 수 있습니다.

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
            console.log('중년의 위기');
        }
    }
}
```

Private 데이터를 저장하기 위해 위크맵을 사용해서 좋은 점은 `Reflect.ownKeys()`를 사용해도 프로퍼티 이름을 드러내지 않는다는 것입니다.

```javascript
> const person = new Person(50);
> person.incrementAge(); // '중년의 위기'
> Reflect.ownKeys(person); // []
```

위크맵을 사용하는 더욱 실질적인 예는 DOM 요소 자체를 훼손시키지 않고도 DOM 요소에 관련된 데이터를 저장하는 것입니다.

```javascript
let map = new WeakMap();
let el  = document.getElementById('someElement');

// 요소에 대한 약한 참조(weak reference)를 저장
map.set(el, '참조');

// 요소의 값에 접근
let value = map.get(el); // '참조'

// 참조 제거
el.parentNode.removeChild(el);
el = null;

value = map.get(el); // undefined
```

위에서 보여준 대로, 객체가 가비지 콜렉터에 의해 한 번 제거된 다음에는 위크맵이 자동적으로 해당 객체에 의해 식별되는 key-value 쌍을 제거합니다.

> **Note**: 더 나아가, 이 예제의 유용함을 보여주기 위해 jQuery가 참조를 가진 DOM 요소에 대응되는 객체의 캐시를 저장하는 방법을 생각해보세요. 위크맵을 사용하면, jQuery는 문서에서 지워진 특정 DOM 요소에 관련된 모든 메모리를 자동적으로 절약할 수 있습니다. 전반적으로, 위크맵은 DOM 요소를 감싸는 모든 라이브러리에 매우 유용합니다.

<sup>[(목차로 돌아가기)](#목차)</sup>

## Promise

Promise는 다음과 같이 수평적인 코드(콜백 지옥)의 형태를 바꿀 수 있게 해줍니다.

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

수직적인 코드로 바꾸면,

```javascript
func1(value1)
    .then(func2)
    .then(func3)
    .then(func4)
    .then(func5, value5 => {
        // Do something with value 5
    });
```

ES6 이전엔, [bluebird](https://github.com/petkaantonov/bluebird) 혹은 [Q](https://github.com/kriskowal/q)같은 라이브러리를 사용했었습니다. 이제는 Promise가 네이티브로 지원됩니다.

```javascript
new Promise((resolve, reject) =>
    reject(new Error('Promise가 제대로 동작하지 않았습니다!')))
        .catch(reason => console.log(reason));
```

Promise가 제대로 동작(**fulfill**)했을 때 호출되는 **resolve** 메소드와 Promise가 제대로 동작하지 않(**rejected**)았을 때 호출되는 **reject** 메소드를 이용해 Promise를 다룰 수 있습니다.

> **Promise의 장점**: 중첩된 콜백 코드에서는 에러 핸들링하기가 혼란스럽습니다. Promise를 사용하면 에러를 적절히 위로 깨끗하게 전파할 수 있습니다. 게다가, resolve/reject된 후의 Promise의 값은 불변입니다.

아래는 Promise를 사용하는 실질적인 예제입니다.

```javascript
var request = require('request');

return new Promise((resolve, reject) => {
  request.get(url, (error, response, body) => {
    if (body) {
        resolve(JSON.parse(body));
      } else {
        resolve({});
      }
  });
});
```

또한 `Promise.all()`을 사용해서 비동기 동작들의 배열을 다루는 Promise를 **병렬화**할 수 있습니다.

```javascript
let urls = [
  '/api/commits',
  '/api/issues/opened',
  '/api/issues/assigned',
  '/api/issues/completed',
  '/api/issues/comments',
  '/api/pullrequests'
];

let promises = urls.map((url) => {
  return new Promise((resolve, reject) => {
    $.ajax({ url: url })
      .done((data) => {
        resolve(data);
      });
  });
});

Promise.all(promises)
  .then((results) => {
    // Do something with results of all our promises
 });
```

<sup>[(목차로 돌아가기)](#목차)</sup>

## 제너레이터(Generator)

[Promise](https://github.com/DrkSephy/es6-cheatsheet/blob/master/README_ko.md#promise)를 이용해 [콜백 지옥](http://callbackhell.com/)을 피하는 것과 비슷하게, 제너레이터도 비동기적인 동작을 동기적인 느낌으로 만들어서 코드를 평평(flat)하게 만들 수 있도록 해줍니다. 제너레이터는 근본적으로 코드의 [실행을 중지](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/yield)하고 나중에 표현식의 값을 돌려주는 함수입니다.

다음은 제너레이터를 사용하는 간단한 예입니다.

```javascript
function* sillyGenerator() {
    yield 1;
    yield 2;
    yield 3;
    yield 4;
}

var generator = sillyGenerator();
> console.log(generator.next()); // { value: 1, done: false }
> console.log(generator.next()); // { value: 2, done: false }
> console.log(generator.next()); // { value: 3, done: false }
> console.log(generator.next()); // { value: 4, done: false }
```
[next](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator/next)를 사용하면 제너레이터를 전진시키고 새로운 표현식을 계산합니다. 위의 예제는 극히 부자연스럽지만, 다음과 같이 제너레이터는 비동기적인 코드를 동기적인 방식으로 작성하는데에 활용할 수 있습니다.

```javascript
// 제너레이터를 이용해 비동기 동작을 숨김

function request(url) {
    getJSON(url, function(response) {
        generator.next(response);
    });
}
```

그리고 데이터를 돌려줄 제너레이터 함수를 작성합니다.

```javascript
function* getData() {
    var entry1 = yield request('http://some_api/item1');
    var data1  = JSON.parse(entry1);
    var entry2 = yield request('http://some_api/item2');
    var data2  = JSON.parse(entry2);
}
```

`yield` 덕분에, `data1`에 데이터가 파싱될 필요가 있을 때에만 `entry1`이 데이터를 가질 것임을 보장할 수 있습니다.

제너레이터는 비동기적인 코드를 동기적인 방식으로 작성하는데 도움을 주지만, 에러 전파는 깨끗하지 않고 쉽지 않은 경로를 통해 해야 합니다. 그렇기 때문에, Promise를 통해 제너레이터를 보완할 수 있습니다.

```javascript
function request(url) {
    return new Promise((resolve, reject) => {
        getJSON(url, resolve);
    });
}
```

그리고 `next`를 사용하는 제너레이터를 통해 단계별로 진행하는 함수를 작성합니다. `next`는 `request` 메소드를 사용하여 위의 Promise를 돌려줍니다.

```javascript
function iterateGenerator(gen) {
    var generator = gen();
    (function iterate(val) {
        var ret = generator.next();
        if(!ret.done) {
            ret.value.then(iterate);
        }
    })();
}
```

Promise를 통해 제너레이터를 보완함으로서, Promise의 `.catch`와 `reject`를 활용해 에러 전파를 깨끗하게 할 수 있게 되었습니다. 다음과 같이 새롭게 개선된 제너레이터를 사용하면 전보다 더 간단합니다.

```javascript
iterateGenerator(function* getData() {
    var entry1 = yield request('http://some_api/item1');
    var data1  = JSON.parse(entry1);
    var entry2 = yield request('http://some_api/item2');
    var data2  = JSON.parse(entry2);
});
```

구현했던 코드는 전처럼 제너레이터를 사용하기 위해서 재활용할 수 있습니다. 제너레이터와 Promise가 비동기적인 코드를 동기적인 방식으로 작성하면서도 에러 전파를 좋은 방법으로 하도록 유지시킬 수 있도록 도와줬지만, 사실, [async-await](https://github.com/DrkSephy/es6-cheatsheet/blob/master/README_ko.md#async-await)는 같은 이점을 더 간단한 형태로 활용할 수 있도록 제공합니다.

<sup>[(목차로 돌아가기)](#목차)</sup>

## Async Await

사실 `async await`는 곧 나올 ES2016의 기능이지만, 제너레이터와 Promise를 같이써야 할 수 있었던 것들을 더 적은 노력으로 가능하게 합니다.

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
  console.log(data); // undefined 값이 아님!
}

main();
```

간단한 구현이지만, 제너레이터와 비슷하게 동작하는 코드입니다. 저는 제너레이터 + Promise보다는 `async-await`를 사용하는 것을 강력하게 추천드립니다. [여기](http://masnun.com/2015/11/11/using-es7-asyncawait-today-with-babel.html)에서 ES7과 Babel을 통해 실행할 수 있는 좋은 예제를 얻을 수 있습니다.

<sup>[(목차로 돌아가기)](#목차)</sup>

## Getter와 Setter 함수

ES6는 getter와 setter 함수를 지원하기 시작했습니다. 다음 예제를 보세요.

```javascript
class Employee {

    constructor(name) {
        this._name = name;
    }

    get name() {
      if(this._name) {
        return this._name.toUpperCase() + ' 양';
      } else {
        return undefined;
      }
    }

    set name(newName) {
      if (newName == this._name) {
        console.log('이미 같은 이름을 쓰고 있습니다.');
      } else if (newName) {
        this._name = newName;
      } else {
        return false;
      }
    }
}

var emp = new Employee("솔지");

// 내부적으로 get 메소드를 활용
if (emp.name) {
  console.log(emp.name);  // 솔지 양
}

// 내부적으로 setter를 활용
emp.name = "EXID 솔지";
console.log(emp.name);  // EXID 솔지 양
```

가장 최근의 브라우저들은 객체의 getter와 setter 함수를 지원합니다. set/get 전에 리스너와 전처리작업을 추가하여 계산된 프로퍼티를 위해 getter와 settter를 활용할 수 있습니다.

```javascript
var person = {
  firstName: '솔지',
  lastName: '허',
  get fullName() {
      console.log('이름 Get');
      return this.lastName + ' ' + this.firstName;
  },
  set fullName (name) {
      console.log('이름 Set');
      var words = name.toString().split(' ');
      this.lastName = words[0] || '';
      this.firstName = words[1] || '';
  }
}

person.fullName; // 허 솔지
person.fullName = 'EXID 솔지';
person.fullName; // EXID 솔지
```
<sup>[(목차로 돌아가기)](#목차)</sup>

## License

The MIT License (MIT)

Copyright (c) 2015 David Leonard

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

<sup>[(목차로 돌아가기)](#목차)</sup>