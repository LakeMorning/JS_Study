# Basic

built-in varialble type:
* `string`
* `number`
* `boolean`
* `null` and `undefined`
* `object`
* `symbol` (new to ES6)

note: 
```js
typeof null === "object"; // true
```


# Scope & Closures

## Cheating Lexical scope

The `eval(..)` function in JavaScript takes a string as an argument, and treats the contents of the string as if it had actually been authored code at that point in the program. In other words, you can programmatically generate code inside of your authored code, and run the generated code as if it had been there at author time.

```js
function foo(str, a) {
    eval( str ); // cheating!
    console.log( a, b );
}

var b = 2;

foo( "var b = 3;", 1 ); // 1 3
```

The string `"var b = 3;"` is treated, at the point of the `eval(..)` call, as code that was there all along. Because that code happens to declare a new variable `b`, it modifies the existing lexical scope of `foo(..)`. In fact, as mentioned above, this code actually creates variable `b` inside of `foo(..)` that shadows the `b` that was declared in the outer (global) scope.

When the `console.log(..)` call occurs, it finds both `a` and `b` in the scope of `foo(..)`, and never finds the outer `b`. Thus, we print out "1 3" instead of "1 2" as would have normally been the case.

**Peformance issue**: Most of JavaScript *Engine*'s optimizations *would* be pointless if `eval(..)` is present, so it simply doesn't perform the optimizations *at all*. Your code will almost certainly tend to run slower simply by the fact that you include an `eval(..)` anywhere in the code.

## Anonymous function expression && Invoking Function Expressions Immediately

Function expressions can be anonymous:
```js
setTimeout(function(){
    console.log("I waited 1 second!");
}, 1000 );
```

IIFEs don’t need names, necessarily—the most common form of IIFE is to use an anonymous function expression. But naming an IIFE has all the aforementioned benefits over anonymous function expressions, so it’s a good practice to adopt.
```js
var a = 2;

(function IIFE(def){
    def(window);
})(function def(global){
    var a = 3;
    console.log(a); // 3
    console.log(global.a); // 2
});
```

## block scope vs function scope

### [var vs let](https://stackoverflow.com/questions/762011/whats-the-difference-between-using-let-and-var-to-declare-a-variable-in-jav)
The difference is scoping. var is scoped to the nearest function block and let is scoped to the nearest enclosing block, which can be smaller than a function block. Both are global if outside any block.

Also, variables declared with let are not accessible before they are declared in their enclosing block. As seen in the demo, this will throw a ReferenceError exception.

**1. Global**:

They are very similar when used like this outside a function block.
```js
let me = 'go';  // globally scoped
var i = 'able'; // globally scoped
```
However, global variables defined with let will not be added as properties on the global window object like those defined with var.

```js
console.log(window.me); // undefined
console.log(window.i); // 'able'
```
**2. Function**:

They are identical when used like this in a function block.
```js
function ingWithinEstablishedParameters() {
    let terOfRecommendation = 'awesome worker!'; //function block scoped
    var sityCheerleading = 'go!'; //function block scoped
}
```
 
**3. Block**:

let is only visible in the for() loop and var is visible to the whole function.
```js
function allyIlliterate() {
    //tuce is *not* visible out here
    for( let tuce = 0; tuce < 5; tuce++ ) {
        //tuce is only visible in here (and in the for() parentheses)
        //and there is a separate tuce variable for each iteration of the loop
    }
    //tuce is *not* visible out here
}

function byE40() {
    //nish *is* visible out here
    for( var nish = 0; nish < 5; nish++ ) {
        //nish is visible to the whole function
    }
    //nish *is* visible out here
}
```
**4. Redeclaration**:

Assuming strict mode, var will let you re-declare the same variable in the same scope. On the other hand, let will not:
```js
'use strict';
let me = 'foo';
let me = 'bar'; // SyntaxError: Identifier 'me' has already been declared
```
```js
'use strict';
var me = 'foo';
var me = 'bar'; // No problem, `me` is replaced.
```

## Hoisting
Hoisting is JavaScript's default behavior of moving variable and function declarations to the top. (putting  declarations into memory before it executes any code segment)

* Function body is hoisted along with function declaration.
* Assignment part of _variable_ and _function expressions_ are **not** hoisted.
* Hoisting is per-scope.

Examples:
```js
foo(); // TypeError 
bar(); // ReferenceError
var foo = function bar() { 
    // ...
};
// foo() is attempting to invoke the undefined value, which is a TypeError illegal operation.
```
_Function declarations that appear inside of normal blocks typically hoist to the enclosing scope, rather than being conditional as this code implies:_

```js
foo(); // "b"

var a = true;
if (a) {
   function foo() { console.log( "a" ); }
}
else {
   function foo() { console.log( "b" ); }
}
```

## Scope Closure

> Closure is when a function is able to remember and access its lexical scope even when that function is executing outside its lexical scope.

```js
function foo() {
	var a = 2;

	function bar() {
		console.log( a );
	}

	return bar;
}

var baz = foo();

baz(); // 2 -- Whoa, closure was just observed, man.
```

Whatever facility we use to *transport* an inner function outside of its lexical scope, it will maintain a scope reference to where it was originally declared, and wherever we execute it, that closure will be exercised.

### Loop and closure

```js
for (var i=1; i<=5; i++) {
    setTimeout( function timer(){ console.log( i );}, i*1000 );
}
// output: five 6;
```

All five functions created, though they are defined separately in each loop iteration, are closed over the same shared global scope, which has, in fact, only one i in it.

a fix using IIFE:
```js
for (var i=1; i<=5; i++) {
    (function(j) {
        setTimeout( function timer() {
            console.log( j );
        }, j * 1000 );
    })( i );
}
```

another fix with block scope:
```js
for (let i=1; i<=5; i++) {
    setTimeout( function timer(){
        console.log( i );
    }, i*1000 );
}
```

### Modules

remain...


# Type & Grammer

# Type:

built-in varialble type:
* `string`
* `number`
* `boolean`
* `null` and `undefined`
* `object`
* `symbol` (new to ES6)

Note: 
* null:
    ```js
    typeof null === "object"; // true
    ```
* a function is referred to as a "callable object" -- an object that has an internal `[[Call]]` property that allows it to be invoked.

* Arrays:
    ```js
    typeof [1,2,3] === "object"; // true
    ```

## Value:

Javascript array:
* No type-enforcement
* auto update length
* delete will not update length

String:
* immutable
* not exectly array of characters
=> changing string in place requires convert string into array.

Number:
...

Special value:

1. The Non-value Values

    * `null` is an empty value
    * `undefined` is a missing value

    Or:

    * `undefined` hasn't had a value yet
    * `null` had a value and doesn't anymore

2. Special number
    * The not number, number(NaN)
     NaN is a kind of “sentinel value” that represents a special kind of error condition within the number set.
        ```js
        var a = 2 / "foo";

        a == NaN;	// false
        a === NaN;	// false
        ```
        instead:
        ```js
        var a = 2 / "foo";
        var b = "foo";

        Number.isNaN( a ); // true
        Number.isNaN( b ); // false -- phew!
        ```
    * Negatice zero
        ```js
        var a = 0;
        var b = 0 / -3;

        b.toString() //"0"

        a == b;		// true
        -0 == 0;	// true

        a === b;	// true
        -0 === 0;	// true

        0 > -0;		// false
        a > b;		// false
        ```
**Value vs. Reference**

```js
var a = 2;
var b = a; // `b` is always a copy of the value in `a`
b++;
a; // 2
b; // 3

var c = [1,2,3];
var d = c; // `d` is a reference to the shared `[1,2,3]` value
d.push( 4 );
c; // [1,2,3,4]
d; // [1,2,3,4]
```

Simple values (aka scalar primitives) are *always* assigned/passed by value-copy: `null`, `undefined`, `string`, `number`, `boolean`, and ES6's `symbol`.

Compound values -- `object`s (including `array`s, and all boxed object wrappers -- see Chapter 3) and `function`s -- *always* create a copy of the reference on assignment or passing.

_Since references point to the values themselves and not to the variables, you cannot use one reference to change where another reference is pointed:_

```js
function foo(x) {
	x.push( 4 );
	x; // [1,2,3,4]

	// later
	x = [4,5,6];
	x.push( 7 );
	x; // [4,5,6,7]
}

var a = [1,2,3];

foo( a );

a; // [1,2,3,4]  not  [4,5,6,7]
```