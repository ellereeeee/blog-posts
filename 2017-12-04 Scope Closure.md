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

### Now I Can See

Closure is common when you pass functions around, such as in event handlers, Ajax requests, or callback functions.

For example:

```javascript
function wait(message) {

  setTimeout(function timer() {
    console.log(message);
  }, 1000);
  
}

wait("Hello, closure!");
```
We are executing `timer` outside of its scope, and `timer` can access the `message` variable because of its closure over `timer`.

Simpson says IIFEs aren't examples of closure because they are not executed outside of its own lexical scope, but it can be a common tool to create scope that can be closed over. IIFEs still create scope.

### Loops + Closure

Some might expect `1`, `2`, `3`, `4`, and `5` to be printed from the following code:

```javascript
for (var i=1; i<=5; i++) {
  setTimeout( function timer(){
    console.log( i );
  }, i*1000);
}
```
However, we get `6` printed five times.

This is because the loop is finished long before 1 second. Even if we set the timer to 0, the for loop would still finish first and we would get the same result.

The issue is that the author of that code is implying that each iteration has its own copy of `i`. It does not; there is only one `i` in the global scope.

The following code would remedy this:

```javascript
for (var i=1; i<=5; i++) {
  (function(){
    var j = i;
      setTimeout(function timer(){
        console.log(j);
      }, j*1000);
  })();
}
```
From this code we see we need two things: block scope and closure.

The IIFE gives each iteration its own scope and the `j` variable gives each iteration its own value.

The `let` identifier introduced in ES6 lets us accomplish the same result elegantly. The following code gives the same result:


```
for (let i=1; i<=5; i++) {
  setTimeout(function timer() {
    console.log(i);
  }, i*1000);
}
```

`let` creates block scope from the `for` block. `let` also has a special characteristic where it is reassigned each iteration when it is declared in the header of a `for` loop, so it creates a new scope each iteration and a different variable for each respective scope/iteration. Just like that, `let` accomplishes both block scope and closure.