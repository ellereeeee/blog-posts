These are my notes on "`this` or That?," Kyle Simpson's [first chapter](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch1.md) of _You Don't Know Javascript: **this** and Object Prototypes_.

### Why `this`?

The `this` mechanism provides a more elegant way of implicitly "passing along" an object reference, leading to cleaner API design and easier re-use.

In the example below we are able to use functions `identify` and `speak` against multiple context objects (`me` and `you`) rather than needing a separate version of the function for each object.

```
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
speak( me ); // Hello, I'm KYLE
```

The more complex your usage pattern is, the more clearly you'll see that passing around explicit references (also called context) as an explicit parameter is often messier than passing around a `this` context.