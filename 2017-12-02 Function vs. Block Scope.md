Here are my notes on "Function vs. Block Scope," Kyle Simpson's [third chapter](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20%26%20closures/ch3.md) of _You Don't Know Javascript: Scope and Closures_.

###Scope From Functions###

Functions create scope.

For example:

```
function foo(a) {
	var b = 2;

	// some code

	function bar() {
		// ...
	}

	// more code

	var c = 3;
}
```

`a`, `b`, `c`, and the `bar()` identifiers all belong too `foo`'s scope. They are also accessible to the nested scope of `bar`, unless there are shadowing variables in `bar`'s scope. You cannot access `foo`'s identifiers in the global scope. `console.log(a);` throws a `ReferenceError`.

###Hiding In Plain Scope###

One useful technique to avoid identifiers being overwritten or colliding with each other is to wrap a scope in another scope, such as wrapping a function in another function.

The reasons for this design lies behind the "Principle of Least Privilege," aka "Least Authority," aka "Least Exposure." It says to expose only what is necessary and hide everything else in your code.

Here's a good example of this:

```
function doSomething(a) {
  function doSomethingElse(a) {
    return a - 1;
  }
  
  var b;
  
  b = a + doSomething(a * 2);
  
  console.log(b * 3);
}

doSomething(2); // 15
```

`doSomethingElse` is a functionality of `doSomething`, so it is properly nested in `doSomething`.

If you have to use identifiers with the same name in different layers of scope, be sure to declare them with `var`, otherwise they might collide, be overwritten and cause undesirable results.

"Dependency managers" are tools used in modules that can help prevent colliding scope, but you can achievement the same result without these if you code defensively.

Since variable collision often occurs in the global scope, you could do what many libraries do and create a unique name, typically for an object, and write all the functionalities in the from of properties and methods for that uniquely-named object.
