![A lightbulb.](pictures/lightbulb.jpg)

These are my notes on "`this` All Makes Sense Now!," Kyle Simpson's [second chapter](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch2.md) of _You Don't Know Javascript: this and Object Prototypes_.

### Call-site

The **call-site** is the location in the code where a function is called (not where it's declared).

The **call-stack** is the stack of functions that have been called to get us to the current moment in execution.

When examining what `this` refers to, you must look at the call-site before the currently executing function. This can be found in the call-stack.

You can visualize a call-stack in your mind as a chain of function calls in order. For example, 

```javascript
function baz() {
  // call-stack is: `baz`
  // so, our call-site is in the global scope
  
  console.log( "baz" );
  bar(); // <-- call-site for `bar`
}

function bar() {
  // call-stack is: `baz` -> `bar`
  // so, our call-site is in `baz`
  
  console.log( "bar");
}

baz(); // <-- call-site for `baz`
```

You can find the call-site easily by putting `debugger;` in the first line of the function in question and then checking your browsers' built-in developer tools. The JS debugger will show the call-site in the second item from the top of the call-stack.

### Nothing But Rules

##### Default Binding

The _default binding_ for `this` points at the global object (if the contents of the function with `this` is not running in `strict mode`).

##### Impicit Binding

When a function with `this` belongs to an object, the _implicit binding_ rule says that the object is what is used for the function call's `this` binding. The object is commonly referred to as the "context object."

Only the top/last level of an object property reference chain matters to the call-site. For example:

```javascript
function foo() {
  console.log( this.a );
}

var obj2 = {
  a: 42,
  foo: foo
};

var obj1 = {
  a: 2,
  obj2: obj2
};

obj1.obj2.foo(); // 42
```

One common frustration with the `this` binding is when an _implicitly bound_ function loses that binding and falls back to the _default binding_. For example:

```javascript
function foo() {
  console.log( this.a );
}

var obj = {
  a: 2,
  foo: foo
};

var bar = obj.foo; // function reference/alias!

var a = "oops, global"; // `a` also property on global object

bar(); // "oops, global"
```

The key thing to remember is that **`obj.foo` is a reference to the `foo` function.** Assigning `obj.foo` to `bar` is just assigning the `foo` function reference to `bar`. `foo`'s implicit binding to `obj` is lost and thus the global property `a` is logged to the console.

This also happens often when passing a callback function. If we had:

```javascript
function doFoo(fn) {
  fn(); // <-- call-site!
}
```

And passed `obj.foo` like this: 

```javascript
dooFoo( obj.foo );
```

`"oops, global"` would be logged to the console. `fn` is just another reference to `foo`.

The same thing happens when using a function built into the language;

```javascript
setTimeout( obj.foo, 100 ); // "oops, global"
```

One common real world example of an implicitly lost binding is in the ReactJS framework. An event handler in a class body that is passed into JSX will lose its implicit binding unless you hard bind the handler in the class constructor or define the handler with an arrow function. You can read about this in more detail in my [notes](https://medium.com/@ellereeeee/the-beginners-guide-to-reactjs-notes-part-6-529ab1387cde) on egghead.io's ReactJS tutorial.

##### Explicit Binding

You can directly state what you want your `this` to be with the `call(..)` and `apply(..)` methods. They take an object to use for the `this` in the first parameter then invoke the function with that `this` specified. This is called _explicit binding_.

For example:

```javascript
function foo() {
  console.log( this.a );
}

var obj = {
  a: 2
};

foo.call(obj); // 2
```
_Hard binding_ is a variation of _explicit binding_ where you assign the explicit binding (with `call(..)` or `apply(..)`) to a function reference.

For example:

```javascript
function foo() {
  console.log( this.a );
}

var obj = {
  a: 2
};

var bar = function() {
  foo.call( obj );
};

bar(); // 2
setTimeout( bar, 100 ); // 2
```

`bar` hard binds `foo`'s `this` to `obj`.

_Hard binding_ is such a common pattern that it's provided as a built-in utility as of ES5: `Function.prototype.bind`.

`bind(..)` returns a new function that is hard-coded to call the original function with the `this` context set as you specified.

For example:

```javascript
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

##### `new` Binding

In JS, constructors are **just functions** that happen to be called with the `new` operator in front of them. They are not attached to classes, they do not instantiate a class, and they are not special types of functions. They are regular functions that are hijacked by the use of `new` in their invocation. Any functions can be called with `new` in front of it, making it a _constructor call_.

When a function is invoked with `new` in front of it the following things are done automatically:

1. a brand new object is created (aka, constructed) out of thin air

2. _the newly constructed object is [[Prototype]]-linked_

3. the newly constructed object is set as the `this` binding for that function call

4. unless the function returns its own alternate **object** the `new`-invoked function call will _automatically_ return the newly constructed object

The `new` binding is when we make a constructor call and the new object is the binding for `this`. For example:

```javascript
function foo(a) {
  this.a = a;
}

var bar = new foo( 2 );
console.log( bar.a ); // 2
```

Running `bar;` in the console will show `Object { a: 2 }`.

### Everything In Order

These are the rules for determining `this` by order of precedence.

1.If the function is called with `new` (**new binding**), `this` is the newly constructed object.

```javascript
var bar = new foo()
```

2.If the function is called with `call` or `apply` (**explicit binding**), even inside a `bind` _hard binding_, `this` is the explicitly specified object.

```javascript
var bar = foo.call( obj2 )
```

3.If the function is called with an "owning" or containing (context) object, `this` is that context object.

```javascript
var bar = obj1.foo()
```

4.If none of the above apply `this` is the `global` object or `undefined` in strict mode (**default binding**).

```javascript
var bar = foo()
```

### Binding Exceptions

If you pass `null` or `undefined` as a `this` binding parameter to `call`, `apply`, or `bind` then the _default binding_ rule applies to the invocation.

It's common to use `apply(..)` to spread out arrays of values as parameters to a function call. `bind(..)` can also be used to curry parameters. ES6 has the `...` spread operator which does the same without needing `apply(..)`, but `bind(..)` is still necessary for currying.

One way to avoid accidentally changing the `global` object with the _default binding_ rule is to use an empty placeholder object like `ø = Object.create(null)`.

Be careful of "indirect references" to functions. When this happens, the _default binding_ rule applies. For example:

```javascript
function foo() {
  console.log( this.a );
}

var a = 2;
var o - { a: 3, foo: foo };
var p = { a: 4 };

o.foo(); // 3
(p.foo = o.foo)(); // 2
```

`o.foo` is just a reference to function `foo` in the global scope, so the global variable `a` is logged to the console.

### Lexical `this`

Arrow-functions adopt the `this` binding from the enclosing (function or global) scope. For example:

```javascript
function foo() {
  return (a) => {
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

The arrow-function lexically captures whatever `foo()`s `this` is at its call-time. `bar` is `this`-bound to `obj1` since `foo()` was `this`-bound to `obj1` with `call(..)`. The lexical binding of an arrow-function cannot be overridden.

A program can effectively use both lexical and `this` styles of code, but mixing the two mechanisms inside the same function makes for harder-to-maintain code.