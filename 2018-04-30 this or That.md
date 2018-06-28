These are my notes on "`this` or That?," Kyle Simpson's [first chapter](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch1.md) of _You Don't Know Javascript: this and Object Prototypes_.

### What's `this`

`this` is a binding that is made when a function is invoked. What it references it determined entirely by the call-site where the function is called.

### Why `this`?

The `this` mechanism provides a more elegant way of implicitly "passing along" an object reference, leading to cleaner API design and easier re-use.

In the example below we are able to use functions `identify` and `speak` against multiple context objects (`me` and `you`) rather than needing a separate version of the function for each object.

```javascript
function speak() {
  var greeting = "Hello, I'm " + identify.call(this);
  console.log(greeting);
}

var me = {
  name: "Richard"
};

var you = {
  name: "Reader"
};

identify.call(me); // RICHARD
identify.call(you); // READER

speak.call(me); // Hello, I'm RICHARD
speak.call(you); // Hello, I'm READER
```

Don't worry if you don't understand how `.call` works, we will discuss it later. Below is an example of explicitly passing on context objects:

```
function identify(context) {
	return context.name.toUpperCase();
}

function speak(context) {
	var greeting = "Hello, I'm " + identify( context );
	console.log( greeting );
}

identify( you ); // READER
speak( me ); // Hello, I'm RICHARD
```

The more complex your usage pattern is, the more clearly you'll see that passing around explicit references (also called context) as an explicit parameter is often messier than passing around a `this` context.

### Confusions

Developers often incorrectly assume two different meanings of `this`.

**The first incorrect assumption is that `this` refers to the function itself.**

Consider this code:

```javascript
function foo(num) {
	console.log( "foo: " + num );

	// keep track of how many times `foo` is called
	this.count++;
}

foo.count = 0;

var i;

for (i=0; i<10; i++) {
	if (i > 5) {
		foo( i );
	}
}
// foo: 6
// foo: 7
// foo: 8
// foo: 9

// how many times was `foo` called?
console.log( foo.count ); // 0 -- WTF?
```

In this situation, `count` is actually created in the global scope with the value of `NaN`. We will explore why in chapter 2.

It's possible to solve the issue by relying on lexical scope. You could have a reference to the function object with a lexical identifier (variable) that points to it.

For example,

```javascript
function foo() {
	foo.count = 4; // `foo` refers to itself
}
```

While this solves the problem, people often rely on lexical scope to avoid understanding of `this`. Using the `.call()` method let's us properly use `this`.

```javascript
function foo(num) {
	console.log( "foo: " + num );

	// keep track of how many times `foo` is called
	// Note: `this` IS actually `foo` now, based on
	// how `foo` is called (see below)
	this.count++;
}

foo.count = 0;

var i;

for (i=0; i<10; i++) {
	if (i > 5) {
		// using `call(..)`, we ensure the `this`
		// points at the function object (`foo`) itself
		foo.call( foo, i );
	}
}
// foo: 6
// foo: 7
// foo: 8
// foo: 9

// how many times was `foo` called?
console.log( foo.count ); // 4
```

**The second assumption people make is that `this` refers to function scope.**

Take a look at this code:

```javascript
function foo() {
	var a = 2;
	this.bar();
}

function bar() {
	console.log( this.a );
}

foo(); //undefined
```

The author of this incorrect code is trying to use `this` to create a bridge between the lexical scopes of `foo()` and `bar()`. They think that `bar()` has access to the variable `a` in the inner scope of `foo()`. This is not possible!

Remember: `this` is a binding that is made when a function is invoked. What it references it determined entirely by the call-site where the function is called.