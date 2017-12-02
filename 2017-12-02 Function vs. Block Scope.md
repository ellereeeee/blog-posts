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