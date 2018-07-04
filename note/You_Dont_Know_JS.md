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

foo(a);

a; // [1,2,3,4]  not  [4,5,6,7]
```

## Natives

Here's a list of the most commonly used natives:

* `String()`
* `Number()`
* `Boolean()`
* `Array()`
* `Object()`
* `Function()`
* `RegExp()`
* `Date()`
* `Error()`
* `Symbol()` -- added in ES6!

1. 
    In general, there's basically no reason to use the object form directly. It's better to just let the boxing happen implicitly where necessary. In other words, never do things like `new String("abc")`, `new Number(42)`, etc -- always prefer using the literal primitive values `"abc"` and `42`.
    (Performance reason)

2. 
    `Array()`, `Object()` and `Function()` are optional.

3.
    `RegExp()`, `Date()` and `Error()` are recommended.

## Coersion

Falsy values in JS:

* `undefined`
* `null`
* `false`
* `+0`, `-0`, and `NaN`
* `""`

Truthy values: Everything that is not falsy. ([], {}, function(){}...)

### Implicit coersion:

####  + operator in JS
1. Basic
    * Strings concatenation:
    ```js
    var result = "Hello, " + "World!"; //"Hello, World!"
    ```
    * Numbers arithmetic addition:
    ```js
    var result = 10 + 5; //15
    ```
2. Conversion rules
    > operand + operand = result 
* If at least one operand is an object, it is converted to a primitive value (string, number or boolean);
* After conversion, if at least one operand is string type, the second operand is converted to string and the concatenation is executed;
* In other case both operands are converted to numbers and arithmetic addition is executed.
 
**3. Object to primitive**
* If object type is Date, then toString() method is used;
* In other case valueOf() method is used, if it returns a primitive value;
* In other case (if valueOf() doesn't exist or doesn't return a primitive value), then toString() method is used. This happens most of the times.


_Example 1: Number and string_

```js
var result = 1 + "5"; // "15" 
```

Explanation:

    1. 1 + "1" (The second operand is a string and based on rule 2 the number 1 becomes "1")
    2. "1" + "1" (Strings concatenation)
    3. "15"

The second operand is a string. The first operand is converted from number to string and the concatenation is done.

_Example 2: Number and array_

```js
var result = [1, 3, 5] + 1; //"1,3,51"  
```

Explanation:

    1. [1, 3, 5] + 1 (Using the rule 1, transform the array [1, 3, 5] to a primitive value: "1,3,5")
    2. "1,3,5" + 1 (Using the rule 2, transform the number 1 to a string "1")
    3. "1,3,5" + "1" (Strings concatenation)
    4. "1,3,51"

The first operand is an array, so it is transformed to a primitive string value. At next step the number operand is transformed to string. Then the concatenation between 2 strings is done.

_Example 3: Number and boolean_

```js
var result = 10 + true; //11 
```

Explanation:

    1. 10 + true (Based on rule 3 convert the boolean true to a number 1)
    2. 10 + 1 (Sum two numbers)
    3. 11

Because neither of the operands is a string, the boolean is converted to number. Then the arithmetic addition is performed.

_Example 4: Number and object_

```js
var result = 15 + {}; // "15[object Object]" 
```

Explanation:

    1. "15 + {}" (The second operand is an object. Apply the rule 1 and the object to primitive is a string "[object Object]")
    2. 15 + "[object Object]" (Using rule 2 transform the number 15 to a string "15")
    3. "15" + "[object Object]" (Strings concatenation)
    4. "15[object Object]"

The second object operand is converted to a string value. Because valueOf() method returns the object itself and not a primitive value, the toString() method is used, which returns string. 

The second operand is now a string, thus the number is converted to string too. The concatenation of 2 strings is executed.

_Example 5: Number and null_

```js
var result = 8 + null; // 8
```

Explanation:

    1. 8 + null (Because none of the operands is string, convert the null to a number 0 based on rule 3)
    2. 8 + 0 (Numbers addition)
    3. 8

Because operands are not objects or strings, null is converted to number. Then numbers sum is evaluated.

_Example 6: String and null_

```js
var result = "queen" + null; // "queennull"
```

Explanation:

    1. "queen" + null (Because none first operand is string, convert the null to a string "null" based on rule 2)
    2. "queen" + "null" (Strings concatenation)
    3. "queennull"

Because the first operand is a string, null is converted to string. Then the strings concatenation is done.

_Example 7: Number and undefined_

```js
var result = 12 + undefined; // NaN  
```

Explanation:

    1. 12 + undefined (Because none of the operands is string, convert the undefined to a number NaN based on rule 3)
    2. 12 + NaN (Numbers addition)
    3. NaN

Because neither of the operands is object or string, undefined is converted to number: NaN. Making an addition between number and NaN evaluates always to NaN.

#### Operators `||` and `&&`

Unlike logic operator in other language (java, C++), they are more like "operand selector operators" in JS.
> The value produced by a && or || operator is not necessarily of type Boolean. The value produced will always be the value of one of the two operand expressions.

Let's illustrate:

```js
var a = 42;
var b = "abc";
var c = null;

a || b;		// 42
a && b;		// "abc"

c || b;		// "abc"
c && b;		// null
```

Both `||` and `&&` operators perform a `boolean` test on the **first operand** (`a` or `c`). If the operand is not already `boolean` (as it's not, here), a normal `ToBoolean` coercion occurs, so that the test can be performed.

For the `||` operator, if the test is `true`, the `||` expression results in the value of the *first operand* (`a` or `c`). If the test is `false`, the `||` expression results in the value of the *second operand* (`b`).

Inversely, for the `&&` operator, if the test is `true`, the `&&` expression results in the value of the *second operand* (`b`). If the test is `false`, the `&&` expression results in the value of the *first operand* (`a` or `c`).

### Loose Equals Versus Strict Equals

>"== allows coercion in the equality comparison and === disallows coercion.”

(`NaN` is never equal to itself;
 `+0` and `-0` are equal to each other)

1. Comparing: anything to `boolean`
```js
var a = "42";

// bad (will fail!):
if (a == true) {
	// ..
}

// also bad (will fail!):
if (a === true) {
	// ..
}

// good enough (works implicitly):
if (a) {
	// ..
}

// better (works explicitly):
if (!!a) {
	// ..
}

// also great (works explicitly):
if (Boolean( a )) {
	// ..
}
```

2. Comparing: `null`s to `undefined`s

> 1. If x is null and y is undefined, return true.
> 2. If x is undefined and y is null, return true.

```js
var a = null;
var b;

a == b;		// true
a == null;	// true
b == null;	// true

a == false;	// false
b == false;	// false
a == "";	// false
b == "";	// false
a == 0;		// false
b == 0;		// false
```

3. Comparing: `object`s to non-`object`s

> 8. If Type(x) is either String or Number and Type(y) is Object,
>    return the result of the comparison x == ToPrimitive(y).
> 9. If Type(x) is Object and Type(y) is either String or Number,
>    return the result of the comparison ToPrimitive(x) == y.

```js
var a = "abc";
var b = Object( a );	// same as `new String( a )`

a === b;				// false
a == b;					// true
```

**1. If either side of the comparison can have `true` or `false` values, don't ever, EVER use `==`.
2. If either side of the comparison can have `[]`, `""`, or `0` values, seriously consider not using `==`.**


##Syntax
.....

# This and Object Prototype

## This keyword

### Definition:

`this` is a binding that is made when a function is invoked, and *what* it references is determined entirely by the call-site where the function is called.

call-site: the location in code where a function is called (not where it’s declared).

### Rules:

#### Default Binding

Default binding refers to how `this` is the global context whenever a function is invoked without any of these other rules. If we aren't using a dot and we aren't using `call()`, `apply()`, or `bind()`, our this will be our global object.

Your global context depends on where you're working. If you're in the browser, this will be the window. When programming in strict mode, the global context is undefined.

```js
function foo() {
	console.log( this.a );
}

var a = 2;

foo(); // 2
```

```js
function foo() {
	"use strict";

	console.log( this.a );
}

var a = 2;

foo(); // TypeError: `this` is `undefined`
```

#### Implicit Binding

Implicit binding occurs when dot notation is used to invoke a function.
In implicit binding, **whatever is to the left of the dot** becomes the context for this in the function.

```js
function foo() {
	console.log( this.a );
}

var obj = {
	a: 2,
	foo: foo
};

obj.foo(); // 2
```

#### Explicit Binding

Explicit binding of this occurs when `.call()`, `.apply()`, or `.bind()` are used on a function.

```js
function foo() {
	console.log( this.a );
}

var obj = {
	a: 2
};

foo.call( obj ); // 2
```

##### bind()

When called on a function, .bind() sets a this context and returns a new function of the same name with a bound this context.

```js
function foo(something) {
	console.log( this.a, something );
	return this.a + something;
}

var obj = {
	a: 2
};

var bar = foo.bind( obj );

var b = bar( 3 ); // 2 3
console.log( b ); // 5
```

#### new binding

When a function is invoked with `new` in front of it, otherwise known as a constructor call, the following things are done automatically:

1. a brand new object is created (aka, constructed) out of thin air
2. *the newly constructed object is `[[Prototype]]`-linked*
3. the newly constructed object is set as the `this` binding for that function call
4. unless the function returns its own alternate **object**, the `new`-invoked function call will *automatically* return the newly constructed object.

```js
function foo(a) {
    this.a = a;
}

var bar = new foo( 2 );
console.log( bar.a ); // 2
```

#### Determining `this`

1. Is the function called with `new` (**new binding**)? If so, `this` is the newly constructed object.

    `var bar = new foo()`

2. Is the function called with `call` or `apply` (**explicit binding**), even hidden inside a `bind` *hard binding*? If so, `this` is the explicitly specified object.

    `var bar = foo.call( obj2 )`

3. Is the function called with a context (**implicit binding**), otherwise known as an owning or containing object? If so, `this` is *that* context object.

    `var bar = obj1.foo()`

4. Otherwise, default the `this` (**default binding**). If in `strict mode`, pick `undefined`, otherwise pick the `global` object.

    `var bar = foo()`

#### `this` in arrow function

Instead of using the four standard `this` rules, arrow-functions adopt the `this` binding from the enclosing (function or global) scope.

Let's illustrate arrow-function lexical scope:

```js
function foo() {
	// return an arrow function
	return (a) => {
		// `this` here is lexically adopted from `foo()`
		console.log( this.a );
	};
}

var obj1 = {
	a: 2
};

var obj2 = {
	a: 3
};

var bar = foo.call( obj1 );
bar.call( obj2 ); // 2, not 3!
```

## Object