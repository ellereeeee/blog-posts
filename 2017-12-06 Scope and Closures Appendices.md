![A picture of a laptop and orange juice on a table in a cafe.](pictures/2017-12-06 Scope and Closures Appendices header.jpg)

Here are my notes on [Appendix A: Dynamic Scope](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20%26%20closures/apA.md), [Appendix B: Polyfilling Block Scope](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20%26%20closures/apB.md), and [Appendix C: Lexical-this](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20%26%20closures/apC.md) of Kyle Simpson's book  _You Don't Know Javascript: Scope and Closures_.

### Appendix A: Dynamic Scope

Contrasting lexical scope with dynamic scope can better help us understand lexical scope. Remember that **JavaScript has lexical scope**.

Lexical scope is write-time while dynamic scope is run-time. Lexical scope cares where a function was declared while dynamic scope cares where a function was called from.

This is lexical scope in action in JavaScript:

```javascript
function foo() {
  console.log(a); // 2
}

function bar() {
  var a = 3;
  foo();
}

var a = 2;

bar();
```

Due to lookup rules in lexical scope, `bar()` prints the `2` value of `a` in the global scope. `foo()` first checks the scope of `foo()` for an `a` variable, then checks the global scope where the look-up is resolved.

_theoretical_ dynamic scope in JavaScript would look like this:

```javascript
function foo() {
  console.log(a); // 3 (not 2!)
}

function bar() {
  var a = 3;
  foo();
}

var a = 2;

bar();
```

`3` is printed because `foo()` was called from `bar()`, where it checks for variables in the `bar()` scope and finds the `a` variable with the value of `3`.

Note again that this is a theoretical illustration to help us understand lexical scope. JavaScript has lexical scope!

As a side note, `this` is related to dynamic scope because it cares on how a function was called.

### Appendix B: Polyfilling Block Scope

It is possible to polyfill block scope in pre-ES6 environments with the `try` `catch` statement. Block scope exists in the `catch` clause.

Here is ES6 block scope:

```javascript
{
  let a = 2;
  console.log(a); // 2
}

console.log(a); // Reference Error
```

And here is a `try` `catch` polyfill:

```javascript
try{throw 2}{catch a}{
  console.log(a); // 2
}

console.log(a); // ReferenceError
```

Google has a project called Traceur that transpiles ES6 features into pre-ES6 environments that does something similar to the code above.

To make code blocks more readable, Simpson suggestions writing them explicitly one of two ways:

1) With a comment before the code:

```javascript
/*Let*/ { let a = 2
        console.log(a);
}

console.log(a); // ReferenceError
```

2) Like this readable unsupported syntax that is transpiled with Simpson's tool called _let-er_:

```javascript
let (a = 2) {
  console.log(a); // 2
}

console.log(a); // ReferenceError
```

Try/catch is a better solution than IIFEs for transpiling code blocks because IIFEs property as a function can change the meaning of `this`, `return`, `break`, and `continue`. Such changes could produce undesired results.

If you want to include block scoping in your code, the above tools are available for your use.

### Appendix C: Lexical-this

The arrow function introduced in ES6 (denoted `=>`) has different behavior in regards to how `this` functions inside it.

If you use `setTimeout` (or if any time seems to pass during run-time) with an object method that uses `this`, `this` loses its binding to the object definition it was used in.

If you reference `this` in an arrow function in the object definition, it follows lexical scope rules rather than `this` binding rules. Simpson says this is a workaround for a failure to understand and properly leverage `this` in code. We would have to use `bind()` in the object definition to properly leverage `this`.

This shows that fat arrow functions do more than just make function declarations shorter (in this case they code in a bad practice into JavaScript).

In my opinion, this section would be better placed in the next YDKJS book on the `this` identifier when the reader has a better understanding of both lexical scope and `this`.