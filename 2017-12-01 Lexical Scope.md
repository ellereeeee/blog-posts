Here are my notes on "Lexical Scope," Kyle Simpson's [second chapter](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20%26%20closures/ch2.md) of _You Don't Know Javascript: Scope and Closures_

###Lex-time###

Lexical scope is scope defined at lexing time. JavaScript has lexical scope. In other words, scope is set in stone when variables, functions, and blocks of code are written and processed by the lexer.

![Scope illustrated as bubbles in functions](pictures/scope-figure.png)

Scopes never overlap. Notice this in the figure above.

**Scope look-ups start in the innermost scope bubble of the called function and end at the first match.**

Shadowing is when there are several identifiers with the same name, but have different references.

For example,

```
var a = 2;

function foo() {
  var a = "hello world";
  
  console.log(a);
}
```

The `a` in `foo()` shadows the `a` in the global scope. You could also say the `a` in global scope is shadowed.

If you needed to access the global `a` in an inner scope (i.e. not the global scope), it's possible with `window.a`, since `a` would be a property of the global `window` object in browsers. Non-global shadowed variables cannot be accessed.

This behavior illustrates that scope only applies to first-class identifiers, such as a variable `a`, `b`, or `c`. In something like `foo.bar`, scope would apply to `foo`, then object-property access rules would take over.

The main thing to know is that **lexical scope is only defined by where the function was declared**.