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

