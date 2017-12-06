Here are my notes on [Appendix A: Dynamic Scope](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20%26%20closures/apA.md), [Appendix B: Polyfilling Block Scope](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20%26%20closures/apB.md), and [Appendix C: Lexical-this](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20%26%20closures/apC.md) of Kyle Simpson's book  _You Don't Know Javascript: Scope and Closures_.

### Appendix A: Dynamic Scope

Contrasting lexical scope with dynamic scope can better help us understand lexical scope. Remember that **JavaScript** has lexical scope.

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