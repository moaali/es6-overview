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
+ Keyed collcections (Map, WeakMap, Set, WeakSet)
+ Proxies
+ Symbols
+ Subclasses - Built-ins
+ Binary & Ocatl Literals
+ Promises
+ Reflection
+ Tails Calls
+ Default function parameter
+ Super In Objects
+ Iterators
+ Math
+ Array
+ Number
+ Object
+ String
+ Aync Await
+ Getter/Setter Functions
+ IIFE to Block


### Arrow Function
#### Description
Shorter syntax for function expression with simplified scope rules.
#### Syntax

    ( Function Parameters ) => { Function Body }   // Basic syntax
    () => {} // No prameters   
    ( param1, param2, paramN ) => {} // Multiple prameters
    singleParam => {} // With single pramerter parens are optional
    ( [x, y], [1, 2] ) => {} // Destructuing paramertes
    ( x = 1 ) => {} // default paramerters
    ( ...rest ) => {} // rest parameters
    () => expression // implicit return: with mustaches and return keyword are optional when return single expression
    () => ({ x: 1 }) // for returning objects wrap object inside parens

#### Notes

 1. Arrow function can't be used as constructor function ( i.e. can't bet instaited with `new` keyword ).
 2. Arrow function can't be used as generator.
 3. Arrow function doesn't have a prototype property.
 4. Arrow function doesn't bind `this`, `argument`, `new.target`, `super`.
 5. Arrow function has lexical `this` meaning it inherits `this` from the enclosing context.
 6. Arrow function with `call()`, `apply()`, `bind()` ignores the passed context and only consider the passed arguments.
 7. Arrow function ignores Strict mode rules regard `this` .
 8. Arrow function doesn't have `arguments` object, it inherits it from the enclosing regular function otherwise `RefrenceError` is thrown.
 
 

        // [ Note 3 ]
    let arrow = () => {};
    console.log( arrow.prototype ); // undefined
    
    
    // [ Note 4 ]
    let obj = {
      fn: function () {
        let arrow = () => {
          return this
        }
    
        return arrow();
      }
    };
    
    obj.fn(); // obj not Window
    
    
    // [ Note 6 ]
    var addOne = () => {
      return this.x + 1
    }
    
    var y = {
      x: 0
    }
    
    addOne.call( y ); // NaN not 1 as this === Window
    
    
    // [ Note 7 ]
    let strict = () => { 'use strict'; return this; } // Window not undefined
    let loose  = () => { return this; }               // Window
    
    
    // [ Note 8 ]
    let fn = function ( x ) {
      console.log( arguments[0] )   // x
      let arrow = ( y ) => {
        console.log( arguments[0] ) // x not y
      }
    
      arrow( 1 );
    }
    
    fn( 0 );

