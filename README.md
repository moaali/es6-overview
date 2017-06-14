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
( /* Function Parameters */ ) => { /* Function Body */ }   // Basic syntax

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
 * optional when return single expression
 */
() => expression

// for returning objects wrap object inside parenthesis
() => ({ x: 1 })

// With single parameter parenthesis are optional
singleParam => {} 
```



#### Notes

1. Arrow function can't be used as constructor function ( i.e. can't be instantiated with new keyword ).
2. Arrow function can't be used as generator.
3. Arrow function doesn't have a prototype property.
4. Arrow function doesn't bind this, argument, new.target, super.
5. Arrow function has lexical this meaning it inherits this from the enclosing context.
6. Arrow function with call(), apply(), bind() ignores the passed context and only consider the passed arguments.
7. Arrow function ignores Strict mode rules regard this .
8. Arrow function doesn't have arguments object, it inherits it from the enclosing regular function otherwise RefrenceError is thrown.

```javascript
// [ Note 3 ]
let arrow = () => {}; console.log( arrow.prototype ); // undefined

// [ Note 4 ] let obj = { fn: function () { let arrow = () => { return this }

return arrow();
} };

obj.fn(); // obj not Window

// [ Note 6 ] var addOne = () => { return this.x + 1 }

var y = { x: 0 }

addOne.call( y ); // NaN not 1 as this === Window

// [ Note 7 ] let strict = () => { 'use strict'; return this; } // Window not undefined let loose = () => { return this; } // Window

// [ Note 8 ] let fn = function ( x ) { console.log( arguments[0] ) // x let arrow = ( y ) => { console.log( arguments[0] ) // x not y }

arrow( 1 ); }

fn( 0 );
```




#### Practical usage

Arrow functions are used usually as callback functions, non-method functions or generally at situation where the dynamic binding of `this` and `arguments` isn't needed for example:

1. use as functional programming methods iterator function.
2. use as callback in timing functions.
3. use as Promises callback functions.
4. use as simpler IIFE.

```javascript
// [ Item ]
let arr = [1, 2, 3, 4, 5];
let odd = arr.filter( item => item % 2 !== 0 ); // [1, 3, 5]


// [ Item ]
setTimeout(() => {
  console.log( 'I\'m simple :)' )
}, 100);


// [ Item ]
let p = new Promise((resolve, reject) => {
  // Do your magic
});

p
  .then(data => {
    // Play with data
  })
  .catch(error => {
    // Error handling
  });


// [ Item ]
;(() => {
  // IIFE without passed arguments
})();

;(global => {
  // IIFE with passed arguments
})(window);
``` 
