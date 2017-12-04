Here are my notes on "Scope Closure," Kyle Simpson's [fifth chapter](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20%26%20closures/ch5.md) of _You Don't Know Javascript: Scope and Closures_.

### Nitty Gritty

Closure is when a function is able to remember and access its lexical scope even when that function it executing outside its lexical scope.

Here is a straightforward example:

```javascript
function foo() {
  var a = 2;
  
  function bar() {
    console.log(a);
  }
  
  return bar;
}

var baz = foo();

baz(); // closure is observed!
```

Here we can see that the lexical scope of bar is accessed outside of its own lexical scope.

Since JavaScript's engine gets ride of code in order to reallocate memory efficiently (called the "Garbage Collector" by Simpson), one might expect the inner scope of `foo()` to be gone after it's no longer in use. However, it remains because `bar()` has a closure over the scope of `foo()`. `baz` has access to author-time lexical scope.

Regardless of _how_ we transport an inner function outside of its lexical scope, it will maintain a scope reference to where it was declared and exercise that closure when executed.