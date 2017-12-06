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


```javascript
for (let i=1; i<=5; i++) {
  setTimeout(function timer() {
    console.log(i);
  }, i*1000);
}
```

`let` creates block scope from the `for` block. `let` also has a special characteristic where it is reassigned each iteration when it is declared in the header of a `for` loop, so it creates a new scope each iteration and a different variable for each respective scope/iteration. Just like that, `let` accomplishes both block scope and closure.

### Modules

Module are a powerful code pattern that leverage closure.

Here is an example of a "Revealing Module," the most common module pattern:

```javascript
function CoolModule() {
  var something = "cool";
  var another = [1, 2, 3];
  
  function doSomething() {
    console.log(something);
  }
  
  function doAnother() {
    console.log(another.join("!"));
  }
  
  return {
    doSomething: doSomething,
    doAnother: doAnother
  }
}

var foo = CoolModule();

foo.doSomething(); // cool
foo.doAnother(); // 1 ! 2 ! 3
```
`CoolModule()` is a function that creates scope (and inner scopes and closures) when it is invoked. It is invoked when it is assigned to the `foo` variable.

`CoolModule()` returns an object, which are references to inner functions. The inner variables are kept private. Think of the object as a public API for the module.

There are two requriements for the module pattern:

1) There must be an enclosing outer function, and it must be invoked at least once (each time a new module instance is created, like assigning the outer function to a variable).

2) The enclosing function must have at least one inner function that can access and/or modify a private state (such as changing a private variable).

A module needs closure to be a module. A function with just function properties isn't a module since it would be closing over an empty scope.

You can make only once instance of a module by making the enclosing function an IIFE.

You can also give parameters to the enclosing function, and provide an argument for that parameter when you create an instance.

For example:

```javascript
function CoolModule(id) {
  function identify() {
    console.log(id);
  }
  
  return {
    identify: identify
  };
}

var foo1 = CoolModule("foo 1");
var foo2 = CoolModule("foo 2");

foo1.identify(); // "foo 1"
foo2.identify(); // "foo 2"
```
You can also change the state of the public API by having a reference to it in your inner functions (via object property access to the publicAPI).

For example:

```javascript
var foo = (function CoolModule(id) {
  function change() {
    // modifying the public API
    publicAPI.identify = identify2;
  }
  
  function identify1() {
    console.log(id);
  }
  
  function identify2() {
    console.log(id.toUpperCase());
  }
  
  var publicAPI = {
    change: change,
    identify: identify1
  };
  
  return publicAPI;
})("foo module");

foo.identify(); // foo module
foo.change();
foo.identify(); // FOO MODULE
```

This is a single-instance module due to the IIFE creating one scope. Calling `foo.change()` will change the state of the public API.

Module dependency loaders/managers are modules that leverage closure to create modules and manage dependencies. A "dependency" in this context is when a module needs access to another module to execute a function.

The main point Simpson wants to illustrate is that the modules they produce aren't special. They fulfill the requirements previous discussed for modules.

Simpson provides this simple proof of concept for illustration purposes. I've added comments to briefly illustrate how it makes new modules.

```javascript
var MyModules = (function Manager() { // enclosing function invoked when assigned to `MyModules`
  var modules = {}; // private variables, where new modules are stored
  
  function define(name, deps, impl) {
    for (var i=0; i<deps.length; i++) {
      deps[i] = modules[deps[i]];
    }
    modules[name] = impl.apply(impl, deps); // this is important! New modules are made here.
  }
  
  function get(name) { // use this function to instantiate new modules from the private `modules` variable
    return modules[name];
  }
  
  return { // the public API of the module manager
    define: define,
    get: get
  };
})();
```
Interestingly, the module manager is a module itself.

The following code creates new modules:

```javascript
MyModules.define( "bar", [], function(){
	function hello(who) {
		return "Let me introduce: " + who;
	}

	return {
		hello: hello
	};
} );

MyModules.define( "foo", ["bar"], function(bar){
	var hungry = "hippo";

	function awesome() {
		console.log( bar.hello( hungry ).toUpperCase() );
	}

	return {
		awesome: awesome
	};
} );

var bar = MyModules.get( "bar" );
var foo = MyModules.get( "foo" );

console.log(
	bar.hello( "hippo" )
); // Let me introduce: hippo

foo.awesome(); // LET ME INTRODUCE: HIPPO
```

`define()` creates new modules that are stored in the private variable `modules`. We use the `get()` function of the `MyModules` variable and instantiate new modules (`bar` and `foo`). Again, the main point is that modules produced by module managers are not different than any other module.

ES6 adds first-class syntax for modules. Each module is treated as a separate JavaScript file. Modules can also import other modules, import specific API members, or export their own API members.

Here is an example of this syntax:

```javascript
// this file is titled "bar.js"
function hello(who) {
  return "Let me introduce: " + who;
}

export hello;
```

`export` exports an identifier (a variable or function) to the public API for that module. This is similar to the return object seen in the previously discussed function modules.

Here we import the entire "foo" module:

```javascript
module bar from "bar";

console.log(
  bar.hello("rhino") // Let me introduce: rhino
);
```

`module` imports an entire module API to a bound variable (importing the bar module to the `bar` variable above).

Let's see how we import certain parts of another API:

```javascript
// this file is titled "foo.js"
import hello from "bar";

var hungry = "hippo";

function awesome() {
  console.log(
    hello(hungry).toUpperCase()
  );
}

export awesome;
```
Here we only import `hello()` from the bar module. Note that `hello()` is the only thing in bar's API. If there were other parts and still only imported `hello()`, we would only have access to `hello()` in foo.js. This is similar to the dependencies created by module managers we discussed previously, except dependencies import a whole module to another.

Note that `import` and `export` can be used as many times as necessary in a module's definition.

The contents inside a module file are treated as if enclosed in a scope closure.

To wrap things up, the key thing to remember is that a **closure is when a function can remember and access its lexical scope even when it's invoked outside its lexical scope.**