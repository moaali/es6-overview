# es6-in-a-nutshell
+ arrow function
+ classes
+ object literals extensions
+ template literals
+ assignment destructuring
+ spread operator/ rest parameters
+ let & const + black Scoping
+ for ... of
+ Generators
+ Unicode
+ Modules
+ Keyed collections (Map, WeakMap, Set, WeakSet)
+ Proxies
+ Symbols
+ Subclasses - Built-ins
+ Binary & Octal Literals
+ Promises
+ Reflection
+ Tails Calls
+ Default function parameter
+ Super in Objects
+ Iterators
+ Math
+ Array
+ Number
+ Object
+ String
+ Async Await
+ Getter/Setter Functions
+ IIFE to Block


### Arrow Function

#### Description

Shorter syntax for function expression with simplified scope rules.





#### Syntax

```javascript
// Basic syntax
( /* Function Parameters */ ) => { /* Function Body */ }


// No parameters
() => {}


// Multiple parameters
( param1, param2, paramN ) => {}


// Destructuring parameters
( [x, y], [1, 2] ) => {}


// Default parameters
( x = 1 ) => {}


// Rest parameters
( ...rest ) => {}


/**
 * implicit return: mustaches and return keyword are
 * optional when returning single expression
 */
() => expression


// For returning objects wrap object inside parenthesis
() => ({ x: 1 })


// With single parameter parenthesis are optional
singleParam => {} 
```



#### Notes

1. Arrow function can't be used as **constructor** function ( i.e. can't be instantiated with `new` keyword ).
2. Arrow function can't be used as **generator**.
3. Arrow function doesn't have a `prototype` property.
4. Arrow function doesn't bind `this`, `argument`, `new.target`, `super`.
5. Arrow function has lexical `this` meaning it **inherits `this` from the enclosing context**.
6. Arrow function with `call()`, `apply()`, `bind()` ignores the passed context and only consider the passed arguments.
7. Arrow function ignores Strict mode rules regard `this`.
8. Arrow function doesn't have `arguments` object, it inherits it from the enclosing regular function otherwise `RefrenceError` is thrown.

```javascript
// [ Note 3 ]
let arrow = () => {}; 
console.log( arrow.prototype ); // undefined


// [ Note 4 ]
let obj = { 
  fn: function () { 
    let arrow = () => { return this }
    return arrow();
  }
};

obj.fn(); // obj not Window


// [ Note 6 ] 
var addOne = () => { return this.x + 1 }
var y = { x: 0 }
addOne.call( y ); // NaN not 1 as this === Window


// [ Note 7 ] 
let strict = () => { 'use strict'; return this; } // Window not undefined 
let loose  = () => { return this; } // Window


// [ Note 8 ]
let fn = function ( x ) {
  console.log( arguments[0] )   // x
  let arrow = ( y ) => { 
    console.log( arguments[0] ) // x not y 
  }
  
  arrow( 1 );
}

fn( 0 );
```




#### Practical usage

Arrow functions are used usually as callback functions, non-method functions or generally at situation where the dynamic binding of `this` and `arguments` isn't needed, for example:

1. Use as `Array` functional programming methods iterator function.
2. Use as callback in timing functions.
3. Use as **Promises** callback functions.
4. Use as simpler **IIFE**.

```javascript
// [ Item 1 ]
let arr = [1, 2, 3, 4, 5];
let odd = arr.filter( item => item % 2 !== 0 ); // [1, 3, 5]


// [ Item 2 ]
setTimeout(() => {
  console.log( 'I\'m simple :)' )
}, 100);


// [ Item 3 ]
let p = new Promise((resolve, reject) => {
  // Do your magic
});

p.then(data => {
  // Play with data
});


// [ Item 4 ]
;(() => {
  // IIFE without passed arguments
})();

;(global => {
  // IIFE with passed arguments
})(window);
``` 


### let, const and Block Scoping

#### `let`

##### Description

Used to create a global-scoped or block-scoped variable whose refrence **can** be re-assigned but it can't be re-declared in the same scope.



##### Syntax

```javascript
let identifier [= expression]; // Assignment is optional
```




##### Notes

1. `let` creates global-scoped & block-scoped variable contrary to `var` that creates global-scoped & **function-scoped** only.
2. `let` doesn't create properties on global object contrary to `var`.
3. `let` allows only a single declaration for a sepecific identifier within the same scope. redeclaring the same variable created by `let` within the same scope throws an `SuntaxError`.
4. `let` assignment (a.k.a: intialzation) is **optional** meaninig you can do this `let x;`.
5. `let` gets hoisted but it can't be accessed within the **Temporal Dead Zone[1]** (a.k.a: **TDZ**) means you can't reference the identifier before or within the declaration statement or `RefrenceError` is thrown;

- [1] **Temporal Dead Zone** : The period between scope starting position untill the declaration statement position.

```javascript
// [ Item 1 ]
let x = 0; // global-scoped
function fn() {
  let y = 1; // function-scoped also block-scoped
  if ( y === 1 ) {
    let z = 2; // block-scoped
  }
}


// [ Item 2 ]
let x = 0;
window.x; // undefined not 0


// [ Item 3 ]
let x = 0;
let x = 1; // SyntaxError

let y = 1;
if ( true ) {
  let y = 2; // Totally valid
}


// [ Item 5 ]
let x = 0;

let fn = function () {
  return x;
  let x = 1; // Error below asserts that x is hoisted
};

fn() // RefrenceError
```

##### Practical Usage
- Use `let` whereever you need to re-assign the same identifier to a different value, with loops for instance, otherwise use `const`.



#### `const`


##### Description

The same as `let` with some differences. It is used to create a global-scoped or block-scoped variable whose refrence **can't** be re-assigned and it can't be re-declared in the same scope.



##### Syntax

```javascript
const IDENTIFIER = expression; // Assignment is a must
```




##### Notes

1. `const` creates global-scoped & block-scoped variable contrary to `var` that creates global-scoped & **function-scoped** only, same as `let`.
2. `const` doesn't create properties on global object contrary to `var`, same as `let`.
3. `const` allows only a single declaration for a sepecific identifier within the same scope. redeclaring the same variable created by `const` within the same scope throws a `SyntaxError`. re-assigment of the same identifier at any scope throws a `TypeError`.
4. `const` allows you to delete/write/edit the assigned object.
5. `const` assignment (a.k.a: intialzation) is **a must** meaninig you **can't** do this `const x;` contrary to `let`.
6. `const` gets hoisted but it can't be accessed within the **Temporal Dead Zone** (a.k.a: **TDZ**) means you can't reference the identifier before or within the declaration statement or `RefrenceError` is thrown;

- Common practice when using `const`: capitalize all identifier letters and in case multiple words identifier use `_` as separator.

```javascript
// [ Item 1 ]
const X = 0; // global-scoped
function fn() {
  const Y = 1; // function-scoped also block-scoped
  if ( Y === 1 ) {
    const Z = 2; // block-scoped
  }
}


// [ Item 2 ]
const X = 0;
window.X; // undefined not 0


// [ Item 3 ]
const X = 0;
const X = 1; // SyntaxError
X = 1; // TypeError: re-assigment is not allowed

const Y = 1;
if ( true ) {
  Y = 2; // TypeError: re-assigment is not allowed
  const Y = 2; // Totally valid
}


// [ Item 4 ]
const ARR = [1, 2];
ARR.push(3);    // Valid: ARR = [1, 2, 3]
delete ARR[0];  // Valid: ARR = [undefined, 2, 3]
ARR[0] = 'ONE'; // Valid: ARR = ['ONE', 2, 3]


// [ Item 6 ]
const X = 0;

const FN = function () {
  return x;
  const X = 1; // Error below asserts that x is hoisted
};

FN() // RefrenceError
```

##### Practical Usage
- Use `const` whereever you don't need to re-assign the same identifier to a different value, otherwise use `let`.


#### Promises

##### Description
Promise is the eventual result of async operation.


##### Terms
- **Resolver**: Function that defines `Promise` logic.
- **Thenable**: Object with a `then` method, it may be a `Promise` object or user defined object.
- **Pending**: `Promise` status when it doesn't achieve any result yet.
- **Settlement**: `Promise` status when it achieves any result (success/fail).
- **Fulfillment**: `Promise` has succeeded/resolved.
- **Fail/Reject**: `Promise` is rejected/failed.

[https://i.stack.imgur.com/idOX8.png]



##### API

###### `Constructor`
``` javascript
/**
 * Promise constructor consists of 3 simple parts as following:
 *
 * [1] : Async operation.
 * [2] : Success condition to resolve returned data from async operation.
 * [3] : Fail condition to reject the promise.
 */

const
  promise = new Promise(( resolve, reject ) => {
    // [1]
    const
      data = '';

    // [2]
    data && resolve( data );

    // [3]
    !data && reject( new Error('Error: Promise Rejected!') );
  });
```


###### `then`
``` javascript
/**
 * @param {function} successHandler  receives `[[PromiseValue]]` property value as its argument if `[[PromiseStatus]]` is resolved.
 * @param {function} errorHandler    receives promise passed error as its argument if `[[PromiseStatus]]` is rejected.
 * @return Promise Object.
 */

promise.then( successHandler, errorHandler );
```


###### `catch
``` javascript
/**
 * @param {function} errorHandler  receives promise passed error as its argument if `[[PromiseStatus]]` is rejected.
 * @return Promise Object.
 */

promise.catch( errorHandler );
```


###### `all`
``` javascript
/**
 * @param {array} iterable  array of promises
 * @return Promise Object.
 */

Promise.all( iterable )
```


###### `race`
``` javascript
/**
 * @param {array} iterable  array of promises
 * @return Promise Object.
 */

Promise.race( iterable )
```


###### `resolve`
``` javascript
/**
 * @param {*} arg  value, promise, thenable.
 * @return Promise Object.
 */

Promise.resolve( arg )
```


###### `reject`
``` javascript
/**
 * @param {*} arg  value passed when promise is rejected.
 * @return Promise Object.
 */

Promise.reject( arg )
```


##### Notes
- Inside `Promise` constructor body any error thrown is treated as the rejection value.
- `then`: method used to register callback to receive fulfillment value and/or the reason why the promise is rejected.
- For chainable `then`, each one `successHandler`'s argument equals to the previous `then` return value.
- When a promise is rejected the first `catch` method fires its callback with the promise error as its argument.
- `catch` method doesn't stop further code execution.
- each `then` method throws its error to the next `catch` method in the chain.
- `Promise.catch( errorHandler )` = `Promise.then( null, errorHandler )`.
- 
- Whenever possible try to make promise rejection value as instance of `Error` for debugging purposes.
- `Promise.all`: if only one promise in the collection is rejected then the return promise `[[PromiseStatus]]` will be `rejected`.
- `Promise.race`: the first settled promise is the one who defines the returned promise `[[PromiseStatus]]` & `[[PromiseValue]]`.

### Modules

#### `export`
##### Syntax
``` javascript
export default expression
export { name1, name2 as alias, name3 as default, ... }
export let multiple, names
```

##### Notes
- Only one default value can be exported from the same script.
- The name of the default export can be omitted (i.e. you can export anonymous function).


##### `import`
##### Syntax
``` javascript
import defaultName from './module'
import * as namespace from './module'
import { name1, name2 as alias, ... } from './module'
import defaultName, * as namespace from './module'
import defaultName, { name1, name2 as alias, ... } from './module'
import  './module'
```

##### Notes
- Importing something that wasn't exported will fail.
- If your module code will run once you can ignore all naming rules and just `import './filename'`.

### Classes

#### Description
Syntactic sugar for prototypal inheritance in JavaScript.


#### `class`
Reserved keyword used to define a constructor class.

##### Example

``` javascript
class ClassName {
  // ...
}
```

##### Notes
- `class` name binding inside its scope is `const` binding and `let` binding outside.
- `class` can't be called directly as functions do (i.e. `ClassName()` throws).

#### `constructor()`
Method used once inside a class to define properties and methods on it.

##### Example

``` javascript
class ClassName {
  constructor() {
    this.name = 'ClassName';
  }
}
```

##### Notes
- `constructor` method can't be used with `static` keyword.
- `constructor` method can only be used **once** inside class.
- Methods defined outside `constructor` is set on the class prototype.


#### `extends`
Reserved keyword used when deriving subclasses from a superclass.

##### Example

``` javascript
class Parent {
  // ...
}

class Child extends Parent {
  // ...
}
```

##### Notes
- Methods defined on the super class are accessible by its subclasses.

#### `super`
Reserved keyword used inside subclasses or object literals as a reference for their superclass, or their prototypes in case of object literals.

##### Example

``` javascript
class Parent {
  constructor(kind) {
    this.kind = kind
  }
}

class Child extends Parent {
  constructor(childName, parentKind) {
    super(parentKind);
    this.name = childName;
  }

  sayName() {
    console.log( 'My Name is ' + this.name + ' and I\'m ' + this.kind );
  }
}

let baby = new Child('John', 'Human');
baby.sayName(); // My Name is John and I'm Human
```

``` javascript
const
  Parent = {
    kind: 'Human'
  },

  Child = {
    name: 'John',
    sayName() {
      console.log( 'My Name is ' + this.name + ' and I\'m ' + super.kind )
    }
  };

Object.setPrototypeOf(Child, Parent);
Child.sayName(); // My Name is John and I'm Human
```

##### Notes
- You can't access superclass properties/methods before invoking `super()` method first inside subclass constructor.


#### `static`
Reserved keyword used to define (static methods) methods accessible only by classes themselves not their instances.

##### Example

``` javascript
class Parent {
  static sayName(name) {
    console.log(name);
  }
}

Parent.sayName('Doe'); // Doe

const parentName = new Parent();
parentName.sayName('Doe'); // TypeError
```

##### Notes
- Static methods are reserved for their respective class and can't be accessed inside its instances.



### Template Literals

#### Description
Special type of string literals with the capability of holding embedded expressions and optionally parsed by custom function called tag function.


#### Example
```javascript
let string = `contents`;
```



#### Substitution Expressions (Placeholders)
JavaScript expressions that can be parsed at runtime inside template literals and initiated by `${ expression }`.

##### Example
```javascript
let greeting = 'Hello';
console.log( `${greeting} Developer!` );
```



#### Tagged Template Literals
Custom function used to control parsing template literal.

##### Syntax
```javascript
let parseResult = TAG_FUNCTION_NAME`contents`;
```

##### Example
```javascript
const
  name = 'Neo',
  HTMLEscaped = escape`<div>I'm ${name}</div>`

/**
 * Custom function used to escape HTML angles
 *
 * @param  {array} strings
 *    Array of template literal string parts with `row` property containing
 *    array of the same values but without parsing backslash escape sequences.
 * @param  {array} subs
 *    Array containing all substitution expressions parsed values.
 * @return {string} the newly parsed template literal contents.
 */
function escape( strings, ...subs ) {
  const results = [];

  strings = strings.map((string) => {
    let escapedString = string.replace(/</i, '&lt;').replace(/>/i, '&gt;')
    return escapedString;
  });

  for ( const i of strings ) {
    results.push( i, subs[strings.indexOf(i)] || '' )
  }

  return results.join('');
}

console.log( HTMLEscaped ); // &lt;div&gt;I'm Neo&lt;/div&gt;
```


#### Raw String
Method used to access template literal string contents without parsing backslash escape sequences (i.e. the source code as you type it in the script  file).

##### Example
```javascript
console.log(`My name is ${'NEO'}.\n\nand I've no choice`)
// My name is NEO.
//
// and I've no choice
console.log(String.raw`My name is ${'NEO'}.\n\nand I've no choice`)
// My name is NEO.\n\nand I've no choice
```


### Spread / Rest Operator
#### Spread
Expands and expression in positions where a comma separated list are expected like function call arguments, array elements, destructuring

#### Examples
```javascript
let x = [3, 4]
let y = [1, 2, ...x] // [1, 2, 3, 4]

let args = [1, 2, 3]
invoke(...args) === invoke(1, 2, 3)
```

#### Rest
Used to convert function infinte number of arguments into array in function declarations.

#### Example
```javascript
function add(...args) {
  let total = 0;
  args.map((num) => {
    total += num; 
  });
  
  return total;
}

add(1, 2, 3, 4) // 10
```


### New Statments

#### `for ... of`

##### Description
Statement used to loop over an *iterable object* and provide a hook to each one of its property to do something with.

##### Syntax
for ( value of iterable ) {
  // do some thing using `value`
}

##### Example
```javascript
cont arr = [1, 2, 3]

for (let val of arr) {
  val *= 2
  console.log(val)
} // 2   4   6
```


### Array Methods

#### Methods List
- Array.from
- Array.of
- Array.prototype.fill
- Array.prototype.find
- Array.prototype.findIndex
- Array.prototype.entries
- Array.prototype.keys
- Array.prototype.copyWithin


#### `Array.from`

##### Description
Method used to create an array from array-like & iterable objects.


##### Syntax
```javascript
Array.from( obj,
            mapFunction ?= undefined,
            context ?= enclosing context ) // => array of the provided input
```


##### Examples
```javascript
let
  arrLike  = { 0: 'zero', 1: 'one', length: 2 },
  iterable = 'iterable',
  fn = function () {
    let args = Array.from( arguments );
    console.log( args )
  };

Array.from( arrLike, (val, idx) => (val += ' in digits ' + idx)) // ['zero in digits 0', 'one in digits 1']
Array.from( iterable )// ['i','t','e','r','a','b','l','e']
fn(1, 2) // [1, 2]
```



#### `Array.of`

##### Description
Method used to create an array from its passed arguments regardless of their type.


##### Syntax
```javascript
Array.of(elm0[, elem1, ... elemN]) // => array of arguments
```


##### Examples
```javascript
Array.of(1, 'string', false, undefined, null, new Date(), {x: 0}, [1]) // [1, "string", false, undefined, null, "3030-01-01:)", {x:0}, [1]]
```



#### `Array.prototype.fill`

##### Description
Method used to change array slots values to the passed value from a starting index to end index.


##### Syntax
```javascript
arr.fill( val,
          start ?= 0,
          end   ?= arr.length ) // => new filled array
```


##### Examples
```javascript
[1, 2, 3].fill(0)           // [0,0,0]
[1, 2, 3].fill(0, 1)        // [1,0,0]
[1, 2, 3].fill(0, 1, 2)     // [1,0,3]
[1, 2, 3].fill(0, -1)       // [1,2,0]
[1, 2, 3].fill(0, -1, -2)   // [1,2,3]
[1, 2, 3].fill(0, -5, -10)  // [1,2,3]
```



#### `Array.prototype.find`

##### Description
Method used to find an first element that passes a test function in a given array.


##### Syntax
```javascript
arr.find( testFN(element, index, arr),
          context ?= undefined ) // => the first matched element value
```


##### Examples
```javascript
let
  arr = [1, 2, 3, 4],
  testFN = (elem) => elem % 2 === 0;

// find the first even numer in the array
arr.find(testFN); // 2
```



#### `Array.prototype.findIndex`

##### Description
Method used to find an first element **index** that passes a test function in a given array.


##### Syntax
```javascript
arr.findIndex(testFN(element, index, arr),
              context ?= undefined) // => the first matched element value
```


##### Examples
```javascript
let
  arr = [1, 2, 3, 4],
  testFN = (elem) => elem % 2 === 0;

// find the index of the first even numer in the array
arr.findIndex(testFN); // 1
```



#### `Array.prototype.entries`

##### Description
Method used to create array iterator from key/value pairs of the provided array.


##### Syntax
```javascript
arr.entries() // => Array Iterator
```


##### Examples
```javascript
let it = [1, 2, 3, 4].entries();
it.next().value; // [0, 1]
it.next().value; // [1, 2]
it.next().value; // [2, 3]
it.next().value; // [3, 4]
```



#### `Array.prototype.keys`

##### Description
Method used to create array iterator from indexes of the provided array.


##### Syntax
```javascript
arr.keys() // => Array Iterator
```


##### Examples
```javascript
let it = [1, 2, 3, 4].keys();
it.next().value; // 0
it.next().value; // 1
it.next().value; // 2
it.next().value; // 3
```



#### `Array.prototype.copyWithin`

##### Description
Method used to copy array or array-like elements from one position to another inside the same array.


##### Syntax
```javascript
arr.copyWithin( targetIndex,
                start ?= 0,
                end ?= arr.length ) // => new formated array
```


##### Examples
```javascript
let
  arr = [1, 2, 3, 4, 5],
  arrLike = { 0: 1, 1: 2, 2: 3, length: 3 }

arr.copyWithin(0, 3) // => [4, 5, 3, 4, 5]
[].copyWithin.call(arrLike, 0, 2) // => { 0: 3, 1: 2, 2: 3, length: 3 }
```


### Number Methods

#### Methods List
- Number.isNaN
- Number.isInteger
- Number.isFinite
- Number.parseInt
- Number.parseFloat
- Number.MAX_SAFE_INTEGER
- Number.MIN_SAFE_INTEGER
- Number.isSafeInteger
- Number.EPSILON


#### `Number.isNaN`

##### Description
Checks if a value is **NaN**.


##### Syntax
```javascript
Number.isNaN(value)
```


##### Examples
```javascript
Number.isNaN(0/0) // => true
Number.isNaN(NaN) // => true

Number.isNaN(true)  // => false
Number.isNaN('NaN') // => false
```


#### `Number.isInteger`

##### Description
Checks if a value is **integer**.


##### Syntax
```javascript
Number.isInteger(value)
```


##### Examples
```javascript
Number.isInteger(1)   // => true
Number.isInteger('1') // => true

Number.isInteger(1.1) // => false
```


#### `Number.isFinite`

##### Description
Checks if a value is finite number.


##### Syntax
```javascript
Number.isFinite(value)
```


##### Examples
```javascript
Number.isFinite(1)          // => true
Number.isFinite(2e64)       // => true

Number.isFinite(Infinity)   // => false
Number.isFinite(NaN)        // => false
Number.isFinite(1/0)        // => false
```


#### `Number.parseInt`

##### Description
parses a value as **integer**.


##### Syntax
```javascript
Number.parseInt(value)
```


##### Examples
```javascript
Number.parseInt(1.2)       // => 1
Number.parseInt('1.99')    // => 1
Number.parseInt(NaN)       // => NaN
Number.parseInt(Infinity)  // => NaN
```


#### `Number.parseFloat`

##### Description
parses a value as **floating point number**.


##### Syntax
```javascript
Number.parseFloat(value)
```


##### Examples
```javascript
Number.parseFloat(1.1)      // => 1.1
Number.parseFloat('1.99')   // => 1.99
Number.parseFloat(NaN)      // => NaN
Number.parseFloat(Infinity) // => Infinity
```



#### `Number.MIN_SAFE_INTEGER`

##### Description
Returns the minimum safest interger.


##### Syntax
```javascript
Number.MIN_SAFE_INTEGER // => -(2 ** 53 - 1)
```



#### `Number.MAX_SAFE_INTEGER`

##### Description
Returns the maximum safest interger.


##### Syntax
```javascript
Number.MAX_SAFE_INTEGER // => 2 ** 53 - 1
```


#### `Number.isSafeInteger`

##### Description
Checks if a number is a safe integer meaning it falls between and including -(2 ** 53 - 1) & (2 ** 53 - 1).


##### Syntax
```javascript
Number.isSafeInteger(value)
```



#### `Number.EPSILON`

##### Description
Returns the smallest possible positive number.


##### Syntax
```javascript
Number.EPSILON // => 2 ** -52
```




### Object Methods

#### Methods List

- Object.prototype.__proto__
- Object.is()
- Object.setPrototypeOf()
- Object.assign()
- Object.getOwnPropertySymbols()

#### `Object.prototype.__proto__`
##### Description
Object property that refrence its constructor prototype obejct.

##### Syntax
```javascript
instance.__proto__
```


##### Examples
```javascript
let
  x = function () {},
  y = {};

x.__proto__ === x.constructor.prototype === Function.prototype
y.__proto__ === y.constructor.prototype === Object.prototype
```


#### `Object.is()`
##### Description
Checks equality of its arguments without applying implicit coersion

##### Syntax
```javascript
Object.is(param1, param2)
```


##### Examples
```javascript
Object.is(NaN, NaN) // => true
Object.is(NaN, 0/0) // => true
Object.is(0, -0)    // => false
Object.is({}, {})   // => false
```


#### `Object.setPrototypeOf()`
##### Description
Define an object as a prototype of other object.

##### Syntax
```javascript
Object.setPrototypeOf(obj, prototype);
```


##### Examples
```javascript
const Human = {
  kind: 'human'
};

const Man = {
  gender: 'Man'
}

const me = {
  name: 'Mo'
}

Object.setPrototypeOf(Man, Human);
Object.setPrototypeOf(me, Man);

me // => { kind: 'human', [[gender: 'Man', name: 'Mo']] }

```


#### `Object.assign()`
##### Description
Shallow copies own enumerable properties from one/multiple source object(s) to a traget object.

##### Syntax
```javascript
Object.assign(target, srcObj1, ...)
```


##### Examples
```javascript
const
  x = { a: 1 },
  y = { b: 2 },
  z = Object.assign({}, x, y);

z // => { a: 1, b: 2 }
```


#### `Object.getOwnPropertySymbols()`
##### Description
Returns an array of all symbol properties of an object

##### Syntax
```javascript
Object.getOwnPropertySymbols(obj) // => [Symbol(x), ...]
```


##### Examples
```javascript
const
  obj = {
    [Symbol('a')]: 1,
    [Symbol('b')]: 2
  };

Object.getOwnPropertySymbols(obj) // => [Symbol(a), Symbol(b)]
```


### Collections
#### Map
##### Description
Map Objects = Objects + super powers :).

##### Syntax
```javascript
new Map([iterable])
```
##### Examples
```javascript
/**
 * [1] : Creating a map object using constructor args.
 * [2] : Creating a map object using `set` method.
 */

// [1]
const
  map1 = new Map([['first', 1], [{key: 'object as a key'}, 'object']]);

// [2]
const
  map2 = new Map();

map2.set('first', 1);
map2.set({key: 'object as a key'}, 'object');
```
##### API
###### API List
- **`Map.prototype.size`**: Determines number of map object members.
- **`Map.prototype.set(key, value)`**: Registers a new member in a map object.
- **`Map.prototype.get(key)`**: Retrieves a value of a map object member.
- **`Map.prototype.delete(key)`**: Deletes a map object member.
- **`Map.prototype.clear()`**: Empty map object.
- **`Map.prototype.has(key)`**: Checks for member existence in a map object.
- **`Map.prototype.entries()`**: Provides iterable object contaning all members key/value pairs in array format.
- **`Map.prototype.keys()`**: Provides iterable object contaning all members keys in array format.
- **`Map.prototype.values()`**: Provides iterable object contaning all members values in array format.
- **`Map.prototype.forEach(callbackFn[, thisArg])`**: Loops throught each map object member and apply a callback function on it.

###### API Examples
```javascript
var
  map = new Map([['first', 1], [{key: 'object as a key'}, 'object'], [3, [1, 2]]]);

map.size                            // => 3
map.get('first')                        // => 1
map.has('first')                        // => true
map.entries()                       // => [[Entries]] = [['first', 1], [{key: 'object as a key'}, 'object'], [3, [1, 2]]]
map.keys()                          // => [[Entries]] = ['first', {key: 'object as a key'}, 3]
map.values()                        // => [[Entries]] = [1, 'object', [1, 2]]
map.delete('first')                     // => returns `true` and map [[Entries]] = [[{key: 'object as a key'}, 'object'], [3, [1, 2]]]
map.clear()                         // => map now has no [[Entries]]

const
  myMap = new Map();

myMap.set('first', 1);
myMap.set({key: 'object as a key'}, 'object');

myMap.forEach((val, key, obj) => {
  console.log(`I'm ${obj} object, I have '${val}' as the value of ${key}`)
})
// I'm [object Map] object, I have '1' as the value of first
// I'm [object Map] object, I have 'object' as the value of [object Object]
```
##### Notes
Some of the powers of Map objects:
- Map objects are iterable, it can be used with `for ... of` loop.
- Map objects has dynamic `size` property to determine number of object members.
- Map objects keys can be any type *primitive or compound*.
- Map objects has direct check for member existence via `has` method.

#### WeakMap
WeakMap objects are the same as Map objects with the following exceptions:
1. WeakMap object **keys must be objects**.
2. WeakMap object members are *weakly refrenced* meaning if they haven't non refrences they will be garbage collected.

#### Set
##### Description
Indexed data structure with unique values, same as arrays but without duplicates.

##### Syntax
```javascript
new Set([iterable])
```

##### Examples
```javascript
const
  set = new Set([1, 2, 3, 3, 3]);

set // => [[Entries]] : [1, 2, 3]
```

##### API
`Set` has the same API as `Map` Object above except that `set` method is replaced with `add` method to add members to the object, also it doesn't have a `get` method.

###### API Examples
```javascript
const
  set = new Set([1, 2]);

set.add(3) // => [[Entries]] : [1, 2, 3]

// You can't directly access set content.
const
  arr = [...set]

arr[0]
```

##### Notes
- Think of Set behavior as arrays but without the duplications.
- You can't directly access `set` content see above example for accesing `set` members.


#### WeakSet
WeakSet objects are the same as Set objects with the following exceptions:
1. WeakSet object **values must be objects**.
2. WeakSet object members are *weakly refrenced* meaning if they haven't non refrences they will be garbage collected.



### Iterables & Iterators

#### Description
- **Iterable**: Object with `[Symbol.iterator]` method as own property which when called returns an iterator.
- **Iterator**: Object containing `next` method as own property which when called returns object with two properties:
  - **`value`**: Holds current value of the iterator.
  - **`done`**: Boolean defines iteration process end.



#### Examples
```javascript
/* For Demo Purposes */
const
  iterator = {
    // ...
    next: () => {
      // ...
      return {
        value : /* Any JavaScript value */,
        done  : /* ture | false */
      }
    }
  },

  iterable = {
    // ...
    [Symbol.iterator]: () => {
      // ...
      return iterator
    }
  }
```



#### Iterable Objects
- **`Array`**
- **`TypedArray`**
- **`Map`**
- **`Set`**
- **`NodeList`**
- Objects returned from `entries()`, `keys()`, `values()` methods.



#### Iterable Consumers
- **`for ... of`** loop
- **Spread Operator**
- **Destructuring**

##### Examples
```javascript
const arr = [1, 2, 3];

for (elem of arr) {
  console.log(elem) // 1, 2, 3
}

const add = (a, b, c) => a + b + c;
add(...arr) // 6

const [a, b, c] = arr;
console.log(a) // => 1
console.log(b) // => 2
console.log(c) // => 3
```


### Generators

#### Description
Functions able to pause/resume its body execution. It returns iterable generator object that can be used to step through execution process. pause/resume points are defined by `yield` expression.


#### Syntax
```javascript
function* generator() {
  statements;
}
```



#### Examples
```javascript
function* gen() {
  yield 1;
  yield 2;
  yield 3;
}

const x = gen();

x.next().value // => 1
x.next().value // => 2
x.next().value // => 3
x.next().value // => undefined
```

### Symbols

#### Description
Function that returns unique identifier of `symbol` primitive type for each call.


#### Syntax
```javascript
Symbol('optional string value')
```


#### Usage
- One of the usual practical usages of `Symbol()` function is generate unique identifiers for object properties.



#### Examples
```javascript
const obj = {
  [Symbol()]    : 1,
  [Symbol('y')] : 2,
  [Symbol('z')] : 3,
  [Symbol('z')] : 4
}

const
  syms  = Object.getOwnPropertySymbols(obj),
  symZ1 = obj[syms[2]],
  symZ2 = obj[syms[3]];

console.log(syms)    // => [Symbol(), Symbol(y), Symbol(z), Symbol(z)]
console.log(syms[2]) // => Symbol(z)
console.log(syms[3]) // => Symbol(z)
console.log(symZ1)   // => 3
console.log(symZ2)   // => 4
```




#### API

1. **`Symbol.iterator`**
2. **`Symbol.match`**
3. **`Symbol.replace`**
4. **`Symbol.search`**
5. **`Symbol.split`**
6. **`Symbol.hasInstance`**
7. **`Symbol.isConcatSpreadable`**
8. **`Symbol.unscopables`**
9. **`Symbol.species`**
10. **`Symbol.toPrimitive`**
11. **`Symbol.toStringTag`**
12. **`Symbol.for(key)`**
13. **`Symbol.keyFor(sym)`**


##### **`Symbol.iterator`**

###### Description
Method used to define iteration behavior of its owner object.



###### Syntax
```javascript
const obj = {
  *[Symbol.iterator]() {
    // ...
  }
}
```



###### Examples
```javascript
const evenIt = {
  *[Symbol.iterator]() {
    for (let i = 0;; i += 2)
      yield i
  }
}


/**
 * Prints even numbers from start to end numbers.
 *
 * @param  {Number} x starting number.
 * @param  {Number} y end number.
 * @return {void}
 */

const print = (x, y) => {
  for (let val of evenIt) {
    if (val >= x && val <= y)
      console.log(val);

    if (val === y)
      break;
  }
}

print(2, 20)
```



###### Notes
- Exists as a build-in method on `Array`, `TypedArray`, `Map`, `Set`.


##### **`Symbol.match`**

###### Description
Method used to define if a RegExp object should be treated as a regular expression or just a normal string


###### Syntax
```javascript
regexp[Symbol.match](str)
```



###### Examples
```javascript
const re = /a/
'/a/'.startsWith(re) // => TypeError -> Because startsWith() expects re to be a string not RegExp.

// Now when modifying the behavior of `re` as string there will be no errors.
re[Symbol.match] = false
'/a/'.startsWith(re) // => true
```



##### **`Symbol.replace`**

###### Description
Method used to define how `String.prototype.replace()` should work.


###### Syntax
```javascript
regexp[Symbol.replace](str, newSubStr) // => true
```



###### Usage
- This method has usually one practical usage which is defining how `String.prototype.replace()` will work when using subclasses of `RegExp` to form its regular expression patterns.



###### Examples
```javascript
class MyRegExp extends RegExp {
  constructor(ptrn, flags) {
    super(ptrn, flags);
  }

  [Symbol.replace](input, replacement) {
    return RegExp.prototype[Symbol.replace].call(this, input, (replacement + ' REPLACEMENT')) // => Every replacement will contain 'REPLACEMENT' now
  }
}


const
  re     = new MyRegExp(/My/, 'i'),
  str    = 'My ',
  newstr = str.replace(re, 'Your');

console.log(newstr); // => 'Your REPLACEMENT'
```



##### **`Symbol.search`**

###### Description
Method used to define how `String.prototype.search()` should work.


###### Syntax
```javascript
regexp[Symbol.search](str)
```



###### Usage
- This method has usually one practical usage which is defining how `String.prototype.search()` will work when using subclasses of `RegExp` to form its regular expression patterns.



###### Examples
```javascript
class MyRegExp extends RegExp {
  constructor(ptrn, flags) {
    super(ptrn, flags);
    this.ptrn = ptrn
  }

  [Symbol.search](str) {
    return `Found At : ${str.indexOf(this.ptrn)}`
  }
}


const
  re  = new MyRegExp('My'),
  str = 'My',
  pos = str.search(re);

console.log(pos); // => Found At : 0
```



##### **`Symbol.split`**

###### Description
Method used to define how `String.prototype.split()` should work.


###### Syntax
```javascript
regexp[Symbol.split](str[, limit])
```



###### Usage
- This method has usually one practical usage which is defining how `String.prototype.split()` will work when using subclasses of `RegExp` to form its regular expression patterns.



###### Examples
```javascript
class MyRegExp extends RegExp {
  [Symbol.split](str, limit) {
    let result = RegExp.prototype[Symbol.split].call(this, str, limit)
    return result.reduce((acc, elem) => acc + elem)
  }
}


const
  re  = new MyRegExp('-'),
  str = 'a-b-c-d',
  res = str.split(re);

console.log(res); // => abcd
```



##### **`Symbol.hasInstance`**

###### Description
Method used to customize the behavior of `instanceof` operator.


###### Syntax
```javascript
[Symbol.hasInstance](instance)
```



###### Usage
- This method has usually one practical usage which is defining how `instanceof` operator will work when defining classes and what objects should be instances of it.



###### Examples
```javascript
class MyObject {
  static [Symbol.hasInstance](ins) {
    return ins.toString() === '[object Object]'
  }
}

console.log({} instanceof MyObject); // => true
```



##### **`Symbol.isConcatSpreadable`**

###### Description
Property with boolean value used to define if the target object (array / array-like) should be spreaded/flattened when used with methods like `Array.prototype.concat()`.


###### Syntax
```javascript
obj[Symbol.isConcatSpreadable] = true/false
```



###### Examples
```javascript
const
  arr = [1, 2, 3],
  arrLike = {
    length: 2,
    0: 'a',
    1: 'b'
  };

arr[Symbol.isConcatSpreadable] = false;
arrLike[Symbol.isConcatSpreadable] = true;

const x = arr.concat(arrLike)
const y = x.concat(arr)

console.log(x) // => [[1, 2, 3], 'a', 'b']
console.log(y) // => [[1, 2, 3], 'a', 'b', [1, 2, 3]]
```



##### **`Symbol.unscopables`**

###### Description
Property with object value used to define which own/inherited object properties should be available inside `with` statsment.


###### Syntax
```javascript
obj[Symbol.unscopables] = { propName: Boolean, ... }
```



###### Examples
```javascript
const
  obj = {
    a: 0,
    b: 1
  };

obj[Symbol.unscopables] = {
  a: false,
  b: true
}

with (obj) {
  console.log(a) // => 0
  console.log(b) // ReferenceError
}
```



##### **`Symbol.species`**

###### Description
Function valued property used to define the constructor of the derived objects from their original objects.


###### Syntax
```javascript
[Symbol.species] () { /* code... */ }
```


###### Usage
- One of the possible usages of `Symbol.species` is make a copy of the built-in objects in JavaScript like `Object`, `Array`, `Promise` ...etc, and add on them your custom implementations without having to polluting the original built-in objects.



###### Examples
```javascript
class MyArray extends Array {
  static get [Symbol.species]() {
    return Array
  }

  isEmpty() {
    return this.length === 0
  }
}

class MyArray2 extends Array {
  isEmpty() {
    return this.length === 0
  }
}

let arr     = new MyArray(1,2,3);   // Original objects
let arr2    = new MyArray2(1,2,3);  // Original objects
let mapped  = arr.map(e => e * 2);  // Derived objects
let mapped2 = arr2.map(e => e * 2); // Derived objects

console.log( mapped instanceof MyArray )   // => false
console.log( mapped instanceof Array )     // => true
console.log( mapped2 instanceof MyArray2 ) // => true
console.log( mapped2 instanceof Array )    // => true
```



##### **`Symbol.toPrimitive`**

###### Description
Function valued property used to define how JavaScript type coercion should work. it takes one argument which is `hint` that can be one of three values `string`, `number`, `default`.


###### Syntax
```javascript
[Symbol.toPrimitive] (hint) { /* code... */ }
```



###### Examples
```javascript
let obj = {}
console.log(+obj)     // => NaN
console.log(`${obj}`) // => "[oject Object]"
console.log(obj + '') // => "[oject Object]"

obj[Symbol.toPrimitive] = hint => {
  switch(hint) {
    case 'number':
      return 1;
      break;
    case 'string':
      return 'obj';
      break;
    case 'default':
      return true;
      break;
  }
}

console.log(+obj)     // => 1
console.log(`${obj}`) // => "obj"
console.log('' + obj) // => "true"
```



##### **`Symbol.toStringTag`**

###### Description
Function valued property used to define a string description of an object. it's used to customize how the `Object.prototype.toString()` method return value.


###### Syntax
```javascript
[Symbol.toStringTag] () { return ... }
```



###### Examples
```javascript
class MyClass1 {}
class MyClass2 {
  get [Symbol.toStringTag] () {
    return 'MyClass2'
  }
}
const x = new MyClass1();
const y = new MyClass2();

x.toString() // => "[object Object]"
y.toString() // => "[object MyClass2]"
                         // ^-------- Tag is now pointing to the return of `@@toStringTag`
```



##### **`Symbol.for`**

###### Description
Checks for the existence of a specified `Symbol` in the global symbols registry and return that `Symbol` if it's found or creates it then return it if not found.


###### Syntax
```javascript
Symbol.for(string) 
```



###### Examples
```javascript
Symbol.for('x') === Symbol.for('x') // => true -> Symbol(x)
Symbol('x') === Symbol('x') // => false
```



##### **`Symbol.keyFor`**

###### Description
Retrieves the key used to create the global registry symbol if found otherwise `undefined`.


###### Syntax
```javascript
Symbol.keyFor(Symbol)
```


###### Examples
```javascript
const
  sym = Symbol.for('x'),
  locSym = Symbol('y'),
  it = Symbol.iterator;

console.log(Symbol.keyFor(sym));    // => X         - found in the global registry
console.log(Symbol.keyFor(locSym)); // => undefined - local symbol not found on the global registry
console.log(Symbol.keyFor(it));     // => undefined - well-known symbol not found on the global registry
```


### Proxy

#### Description
Object used to override/customize default behavior of fundamental operations on other objects.


#### Syntax
```javascript
/**
 * Proxy Object.
 * @param  {*}      target  target object to operate on
 * @param  {Object} handler tarps container
 * @return {Proxy}          Proxy Object
 */
const p = new Proxy(target, handler)
```


#### Practical Usage
Here are some of the practical usages of the `Proxy` object. (VIEW REPO OF THE FOLLOWING EXAMPLES HERE).

1. Listening to changes on a specific object.
2. Object members type validation.
3. Achieving true private members.
4. Revoking access to data.
5. Intercepting certain operations on objects.


#### API

1. **`Proxy.revocable()`**
2. **`handler.getPrototypeOf()`**
3. **`handler.setPrototypeOf()`**
4. **`handler.isExtensible()`**
5. **`handler.preventExtensions()`**
6. **`handler.getOwnPropertyDescriptor()`**
7. **`handler.defineProperty()`**
8. **`handler.has()`**
9. **`handler.get()`**
10. **`handler.set()`**
11. **`handler.deleteProperty()`**
12. **`handler.ownKeys()`**
13. **`handler.apply()`**
14. **`handler.construct()`**


##### **`Proxy.revocable()`**

###### Description
Creates a revocable `Proxy` object.


###### Syntax
```javascript
 /**
 * Creates a revocable Proxy object.
 * @param  {*}      target  target object to operate on
 * @param  {Object} handler tarps container
 *
 * @return {Object}
 *    Object with two props `proxy` containing the Proxy Object
 *    and `revoke` method to terminate the proxy.
 */
Proxy.revocable(target, handler);
```



###### Examples
```javascript
let
  revocable = Proxy.revocable({}, {
    get(target, name) {
      return `GET: "${name}" Property`
    }
  });

let p = revocable.proxy;

console.log(p.a); // => GET: "a" Property

// when calling this method every trap throws a TypeError
revocable.revoke();

console.log(p.a); // => TypeError

```




##### **`handler.getPrototypeOf()`**

###### Description
Intercepts/Customizes `[[GetPrototypeOf]]` operations.
.


###### Syntax
```javascript
/**
 * Intercept [[GetPrototypeOf]] Operations.
 *
 * @param  {Object} target => target object to find prototype for.
 *
 * @return {Object|null}
 *
 * @exceptions
 *   + TypeError
 *     01. Returned value isn't `null` || `Object`.
 *     02. Target object isn't extensible.
 */
handler.getPrototypeOf(target);
```



###### Examples
```javascript
let
  obj   = {},
  proto = {},
  getProto;

getProtoProxy = target => {
  const handler = {
    getPrototypeOf(target) {
      console.log('[[GetProtoType]] Trapped')
      return proto;
    }
  }

  return new Proxy(target, handler);
}

let p = getProtoProxy(obj);
console.log(Object.getPrototypeOf(p)) // => [[GetProtoType]] Trapped {}
```



###### Notes
- `handler.getPrototypeOf()` traps any operation that depends on `[[GetProtoType]]` internal method which will be one of the following:
  1. `Object.getPrototypeOf()`
  2. `Reflect.getPrototypeOf()`
  3. `Object.prototype.isPrototypeOf`
  4. `instanceof`
  5. `__proto__`





##### **`handler.setPrototypeOf()`**

###### Description
Intercepts/Customizes `Object.setPrototypeOf()` operations.


###### Syntax
```javascript
/**
 * Intercept setting `[[Prototype]]` internal property Operations.
 *
 * @param  {Object} target    => target object to set prototype for.
 * @param  {Object} prototype => object to set as prototype for target.
 *
 * @return {Boolean}
 *    true if the prototype is set, false otherwise.
 *
 * @exceptions
 *   + TypeError
 *     01. Target object isn't extensible. So the prototype object should be
 *         the same as `Object.getPrototypeOf()`
 */
handler.setPrototypeOf(target, prototype);
```



###### Examples
```javascript
let
  obj   = {},
  proto = {},
  setProtoProxy;

setProtoProxy = (target, proto) => {
  const handler = {
    setPrototypeOf(target, proto) {
      return true
    }
  };

  return new Proxy(target, proto);
};

const p = setProtoProxy(obj, proto);
console.log(Object.setPrototypeOf(p, null)) // => { a: 1 }
```


###### Notes
- `handler.setPrototypeOf()` traps any operation that depends on setting `[[ProtoType]]` internal property which will be one of the following:
  1. `Object.setPrototypeOf()`
  2. `Reflect.setPrototypeOf()`


##### **`handler.isExtensible()`**

###### Description
Intercepts/Customizes `Object.isExtensible()` operations.


###### Syntax
```javascript
/**
 * Intercept `Object.isExtensible()` Operations.
 *
 * @param  {Object} target => target object to check its extensibility.
 *
 * @return {Boolean}
 *    true if the object is extensible, false otherwise.
 *
 * @exceptions
 *   + TypeError
 *     01. `Object.isExtensible(proxy)` !== `Object.isExtensible(obj)`
 */
handler.isExtensible(target);
```



###### Examples
```javascript
let
  obj      = {},
  nonExObj = {},
  extendProxy;

Object.preventExtensions(nonExObj);

extendProxy = target => {
  const handler = {
    isExtensible(target) {
      console.log('isExtensible is accessed!!');
      return Reflect.isExtensible(target);
    }
  }

  return new Proxy(target, handler)
}

let
  p1 = extendProxy(obj),
  p2 = extendProxy(nonExObj);

console.log(Object.isExtensible(p1)); // => isExtensible is accessed!, true
console.log(Object.isExtensible(p2)); // => isExtensible is accessed!, false
```



##### **`handler.preventExtensions()`**

###### Description
Intercepts/Customizes `Object.preventExtensions()` operation.


###### Syntax
```javascript
/**
 * Intercept `Object.preventExtensions()` Operation.
 *
 * @param  {Object} target => target object to prevent extensibility for.
 *
 * @return {Boolean}
 *    true if the object is extensible, false otherwise.
 *
 * @exceptions
 *   + TypeError
 *     01. `Object.preventExtensions(proxy)` === Object.isExtensible(proxy)
 */
handler.preventExtensions(target);
```



###### Examples

```javascript
let
  obj      = {},
  extendProxy;

extendProxy = target => {
  const handler = {
    preventExtensions(target) {
      console.log('preventExtensions is accessed!!');
      return Reflect.preventExtensions(target)
    }
  }

  return new Proxy(target, handler)
}

let p = extendProxy(obj);

console.log(Object.preventExtensions(p)); // => isExtensible is accessed!, true
```




##### **`handler.getOwnPropertyDescriptor()`**

###### Description
Intercepts/Customizes `Object.getOwnPropertyDescriptor()` operation.


###### Syntax
```javascript
/**
 * Intercept `Object.getOwnPropertyDescriptor()` Operation.
 *
 * @param  {Object} target   => target object to look for property in.
 * @param  {String} property => property to get descriptor for.
 *
 * @return {Object|undefined}
 *    descriptor object for the property or undefined if it doesn't exist.
 *
 * @exceptions
 *   + TypeError
 *     01. Return isn't `Object` or `undefined`.
 *     02. Return undefined for existed property in non-extensible target
 *     03. Return undefined for non-configurable property in target
 */
handler.getOwnPropertyDescriptor(target, property);
```



###### Examples
```javascript
let
  obj  = {a: 1},
  obj2 = {c: 2},
  describeProxy;

Object.preventExtensions(obj2);
Object.defineProperty(obj, 'b', {configurable: false});

describeProxy = (target, prop) => {
  const handler = {
    getOwnPropertyDescriptor(target, prop) {
      console.log('getOwnPropertyDescriptor is accessed!!');
      return undefined;
    }
  }

  return new Proxy(target, handler)
}

let
  p1 = describeProxy(obj, 'a'),
  p2 = describeProxy(obj, 'b'),
  p3 = describeProxy(obj2, 'c');

console.log(Object.getOwnPropertyDescriptor(p1, 'a')) // => Descriptor Object
console.log(Object.getOwnPropertyDescriptor(p2, 'b')) // => TypeError: non-configurable property
console.log(Object.getOwnPropertyDescriptor(p3, 'c')) // => TypeError: non-extensible object
```




##### **`handler.defineProperty()`**

###### Description
Intercepts/Customizes `Object.defineProperty()` operation.


###### Syntax
```javascript
/**
 * Intercept `Object.defineProperty()` Operation.
 *
 * @param  {Object} target     => target object to look for property in.
 * @param  {String} property   => property to define on target object.
 * @param  {Object} descriptor => descriptor object for the specified property.
 *
 * @return {Boolean}
 *    true if the property is set, false otherwise.
 *
 * @exceptions
 *   + TypeError
 *     01. Return false in `strict mode`.
 *     02. Setting configurable existed property to be non-configurable.
 *     03. Adding/modifying non-configurable property that doesn't exist as
 *         non-configurable.
 */
handler.defineProperty(target, property, descriptor);
```



###### Examples
```javascript
let
  obj  = {a: 1},
  defineProxy;

Object.defineProperty(obj, 'b', {configurable: false});

defineProxy = (target) => {
  const handler = {
    defineProperty(target, prop, descriptor) {
      console.log('defineProperty is accessed!!');
      return true;
    }
  }

  return new Proxy(target, handler)
}

let p = defineProxy(obj);

console.log(Object.defineProperty(p, 'a', {configurable: false})) // => TypeError
console.log(Object.defineProperty(p, 'd', {configurable: false})) // => TypeError
console.log(Object.defineProperty(p, 'b', {writable: false})) // => Object
```




##### **`handler.has()`**

###### Description
Intercepts/Customizes `in` operator lookups.


###### Syntax
```javascript
/**
 * Intercepts/Customizes `in` operator lookups.
 *
 * @param  {Object} target     => target object to look for property in.
 * @param  {String} property   => property to look for in target object.
 *
 * @return {Boolean}
 *    true if the property exists, false otherwise.
 *
 * @exceptions
 *   + TypeError
 *     01. Return false if the property exists.
 */
handler.has(target, property);
```



###### Examples
```javascript
let
  obj  = {a: 1},
  hasProxy;

Object.defineProperty(obj, 'b', {configurable: false});

hasProxy = (target) => {
  const handler = {
    has(target, prop) {
      console.log('has is accessed!!');
      return true;
    }
  }

  return new Proxy(target, handler)
}

let p = hasProxy(obj);

console.log('a' in p) // => has is accessed!!, true
```




##### **`handler.get()`**

###### Description
Intercepts/Customizes a property value.


###### Syntax
```javascript
/**
 * Intercepts/Customizes a property value.
 *
 * @param  {Object} target   => target object to look for property in.
 * @param  {String} property => property to look for in target object.
 * @param  {Object} receiver => proxy object.
 *
 * @return {*}
 *    Any value.
 *
 * @exceptions
 *   + TypeError
 *     01. Return different value for non-writable,
 *         non-configurable properties.
 *     02. Not returning `undefined` if the lookup property has value
 *         of `undefined` and non-configurable.
 */
handler.get(target, property, receiver);
```



###### Examples
```javascript
let
  obj  = {a: 1},
  getProxy;

Object.defineProperty(obj, 'b', {configurable: false, writable:false, value: 2});
Object.defineProperty(obj, 'c', {configurable: false});

getProxy = (target) => {
  const handler = {
    get(target, prop, proxy) {
      console.log('get is accessed!!');
      return 1;
    }
  }

  return new Proxy(target, handler)
}

let p = getProxy(obj);

console.log(p.a) // => get is accessed!!, 1
console.log(p.b) // => TypeError
console.log(p.c) // => TypeError
```





##### **`handler.set()`**

###### Description
Intercepts/Customizes setting a property.


###### Syntax
```javascript
/**
 * Intercepts/Customizes setting a property.
 *
 * @param  {Object} target   => target object to look set property on.
 * @param  {String} property => property name.
 * @param  {*}      value    => any value.
 * @param  {Object} receiver => proxy object.
 *
 * @return {Boolean}
 *    True if the assignment process is completed
 *    successfully, false otherwise.
 *
 * @exceptions
 *   + TypeError
 *     01. Setting non-writable, non-configurable properties.
 *     02. Setting non-configurable property that has value of `undefined`.
 *     03. Return false from `set` in `strict mode`.
 */
handler.set(target, property, value, receiver);
```



###### Examples
```javascript
let
  obj  = {a: 1},
  setProxy;

Object.defineProperty(obj, 'b', {configurable: false});
Object.defineProperty(obj, 'c', {configurable: false, writable: false, value: 2});

setProxy = (target) => {
  const handler = {
    set(target, prop, value, proxy) {
      console.log('set is accessed!!');
      return true;
    }
  }

  return new Proxy(target, handler)
}

let p = setProxy(obj);

p.a = 1; // => set is accessed!!, 1
p.b = 1; // => TypeError
```



##### **`handler.deleteProperty()`**

###### Description
Intercepts/Customizes `delete` operator.


###### Syntax
```javascript
/**
 * Intercepts/Customizes `delete` operator.
 *
 * @param  {Object} target   => target object to look set property on.
 * @param  {String} property => property name.
 *
 * @return {Boolean}
 *    True if the delete process is completed
 *    successfully, false otherwise.
 *
 * @exceptions
 *   + TypeError
 *     01. Deleting non-configurable own properties.
 */
handler.deleteProperty(target, property);
```



###### Examples
```javascript
let
  obj  = {a: 1},
  delProxy;

Object.defineProperty(obj, 'b', {configurable: false});

delProxy = (target) => {
  const handler = {
    deleteProperty(target, property) {
      if (property in target) {
        console.log(`Property ${property} successfully deleted!!!`);
        return true
      } else {
        console.log(`Property ${property} not found on ${target} object`);
        return false
      }
    }
  }

  return new Proxy(target, handler)
}

let p = delProxy(obj);

delete p.a; // => Property a successfully deleted!!!, true
delete p.b; // => TypeError
```




##### **`handler.ownKeys()`**

###### Description
Intercepts/Customizes `Reflect.ownKeys` operation.


###### Syntax
```javascript
/**
 * Intercepts/Customizes `Reflect.ownKeys` operation.
 *
 * @param  {Object} target => target object to look for its own keys.
 *
 * @return {Boolean}
 *    Array of own enumerable keys.
 *
 * @exceptions
 *   + TypeError
 *     01. Return isn't an array.
 *     02. Returned array containing values types other than
 *         `String` & `Symbol`.
 *     03. Returned array containing non-configurable own properties.
 *     04. Returned array containing non-existence keys of
 *         non-extensible object.
 */
handler.ownKeys(target);
```



###### Examples
```javascript
let
  obj  = {a: 1},
  ownProxy;

Object.defineProperty(obj, 'b', {configurable: false});

ownProxy = (target) => {
  const handler = {
    ownKeys(target) {
      console.log(`ownKeys is accessed!!`);
      return ['a', 'b', 'c']
    }
  }

  return new Proxy(target, handler)
}

let p = ownProxy(obj);

console.log(Object.getOwnPropertyNames(obj)); // => ownKeys is accessed!!, ['a', 'b', 'c']
```



###### Notes
- `handler.ownKeys` intercepts the following operations:
  1. `Object.keys()`
  2. `Object.getOwnPopertyNames()`
  3. `Object.getOwnPropertySymbols()`
  4. `Reflect.ownKeys()`




##### **`handler.apply()`**

###### Description
Intercepts/Customizes function calls.


###### Syntax
```javascript
/**
 * Intercepts/Customizes function calls.
 *
 * @param  {Object} target => target function to intercept its invocation.
 *
 * @return {*}
 *    Any value.
 */
handler.apply(target);
```



###### Examples
```javascript
let
  fn  = function () { console.log("I'm not gonna show!!") },
  applyProxy;

applyProxy = (target) => {
  const handler = {
    apply(target, thisArg, argsList) {
      console.log(`Function is called`);
    }
  }

  return new Proxy(target, handler)
}

let p = applyProxy(fn);

console.log(p()); // => Function is called
```





##### **`handler.construct()`**

###### Description
Intercepts/Customizes `new` operator.


###### Syntax
```javascript
/**
 Intercepts/Customizes `new` operator.
 *
 * @param  {Function} target   => constructor function/class.
 * @param  {Array}    argsList => array of the passed arguments to constructor.
 * @param  {Object}   proxy    => called proxy constructor.
 *
 * @return {Object}
 *
 * @exceptions
 *   + TypeError
 *     01. Return isn't Object.
 *     02. Target isn't a valid constructor.
 */
handler.construct(target, argsList, proxy);
```



###### Examples
```javascript
let
  C  = function () {},
  constructProxy;

constructProxy = (target) => {
  const handler = {
    construct(target, argsList, proxy) {
      console.log(`Construct is accessed`);
      return {}
    }
  }

  return new Proxy(target, handler)
}

let p = constructProxy(C);

console.log(new p()); // => Construct is accessed, Object {}
```
### Reflect

#### Description
Built-in object that provide methods for interceptable JavaScript operations.
- It's not a function object.
- Not constructible, it does not have a `[[Construct]]` internal method. (Can't use `new Reflect()` ).
- Not invokable, it does not have a `[[Call]]` internal method. (Can't be invoke it as a function `Reflect()`).


#### API
1. **`Reflect.apply()`**
2. **`Reflect.construct()`**
3. **`Reflect.defineProperty()`**
4. **`Reflect.deleteProperty()`**
5. **`Reflect.get()`**
6. **`Reflect.getOwnPropertyDescriptor()`**
7. **`Reflect.getPrototypeOf()`**
8. **`Reflect.has()`**
9. **`Reflect.isExtensible()`**
10. **`Reflect.ownKeys()`**
11. **`Reflect.preventExtensions()`**
12. **`Reflect.set()`**
13. **`Reflect.setPrototypeOf()`**

Here are the API methods equivalent to understand how it works.
###### Syntax
```javascript
Reflect.apply(fn, thisArg, argsArray) <=> Function.prototype.apply.call(Math.floor, undefined, [1.75]);

Reflect.construct(Target, argsList, newTarget) <=> new Target(...argsList);

// Diff: `Reflect` case return Boolean
Reflect.defineProperty(target, property, descriptor) <=> Reflect.defineProperty(target, property, descriptor)

// Diff: `Reflect` case return Boolean
Reflect.deleteProperty(target, property) <=> delete target.property

// Object property accessors in the form of a function.
Reflect.get(target, property, receiver) <=>

// Diff: `Reflect` case return Boolean
Reflect.set(target, property, value, receiver) <=> // Object property accessors in the form of a function.

// Diff: if non-object is set as the target in case of using `Reflect` it will throw a type error.
Reflect.getOwnPropertyDescriptor(target, property) <=> Reflect.getOwnPropertyDescriptor(target, property)

Reflect.getPrototypeOf(target) <=> Object.getPrototypeOf(target)

// same as in operator but it works like a function
Reflect.has(target, property) <=> property in target

// same but target must be an object in `Reflect` or error is thrown
Reflect.isExtensible(target) <=> Object.isExtensible(target)

Reflect.ownKeys(target) <=> Object.getOwnPropertyNames(target).concat(Object.getOwnPropertySymbols(target))

// Return Boolean and target must be object otherwise error is thrown
Reflect.preventExtensions(target) <=> Object.preventExtensions(target)


Reflect.setPrototypeOf(target, prototype) <=> Object.setPrototypeOf(target, prototype)
```

### Destructuring Assigment

#### Description
JavaScript expression that used to assign values from arrays, objects, maps, sets to distinct variables.


#### Examples
```javascript
/**
 * [1] : Using rest parameter to make a sub-array from the main array.
 * [2] : Using Object literal enhancement to set values to keys as seprate variables.
 * [3] : Setting default values to variables.
 * [4] : Swapping values.
 * [5] : Ignoring some values in destructuring array.
 * [6] : Different variable names for properties values.
 * [7] : Computed values with destructuring.
 */

let
  g   = 16,
  h   = 17,
  key = 'm';

let
  [a, b, ...rest]  = [1, 2, 3, 4],   // [1] a = 1, b = 2, rest = [3, 4]
  {c, d}           = {c: 6, d: 7},   // [2] c = 6, d = 7
  {e = 1, f = 2}   = {e: 8, f: 9},   // [3] e = 8, f = 9
  [g, h]           = [h, g],         // [4] g = 17, h = 16
  [i, ,j]          = [10, 11, 12],   // [5] i = 10, j = 12
  {k: foo, l: bar} = {k: 13, l: 14}, // [6] foo = 13, bar = 14
  {[key]: n}       = {m: 15}         // [7] n = 15

```

### Object Literal Enhancements

#### Description
Object literals now supports setting the prototype at construction time, shorthand assignments, shorthand methods, super calls and computed property names.

#### Examples
```javascript
const
  key = 'key',
  prop = 'value',
  Prototype = Object.create(null),
  obj = {
    __proto__: Prototype,      // Setting prototype at construction
    prop,                      // Shorthand property for prop: prop
    method() {}                // Shorthand method for method : function () {}
    toString() {
     return super.toString();  // Super call is supported
    },
    [key]: 42                  // Computed property naming
  };
```


### Subclassable Built-ins

#### Description
Built-in objects now can be subclassed, like `Array`, `Date`, `Object`.


#### Examples
```javascript
class MyArray extends Array {
    constructor(...args) {
      super(...args);
    }

    // Adding new methods to my custom Array constructor
    isEmpty() {
      return this.length === 0
    }
}

let arr = new MyArray();

arr.isEmpty() // => true
