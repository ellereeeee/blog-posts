# Class-inspired Code Design

Let me start off by saying this: **class-inspired code design in JavaScript is convoluted and confusing. Don't do it!** I'll explain why soon.

In this post I'm going to try to explain the thought process of someone from a class-based oriented language (like Python or Ruby) trying to implement a class-inspired approach with JavaScript's `[[Prototype]]` chain. No, **I don't actually come from from a class-based language; JavaScript is my first. This is a mental exercise for myself.**

### Classical (Prototypal) Object Oriented Code Design

I'll be using and explaining examples from Chapter 6: Behavior Delegation of [_YDKJS - this and Object prototype](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch6.md).

Here is an implementation of what Simpson calls a "_classical (prototypal) object oriented style."

```
function Foo(who) {
    this.me = who;
}
Foo.prototype.identify = function() {
    return "I am " + this.me;
};

function Bar(who) {
    Foo.call( this, who );
}
Bar.prototype = Object.create( Foo.prototype );

Bar.prototype.speak = function() {
    alert( "Hello, " + this.identify() + "." );
};

var b1 = new Bar( "b1" );
var b2 = new Bar( "b2" );

b1.speak();
b2.speak();
```

Simpson says: 
> Parent class `Foo`, inherited by child class `Bar`, which is then instantiated twice as `b1` and `b2`.

I copied and pasted the same code below, but changed the variables names in order to make the class-inspired design more apparent.

```
function parentClassFoo(who) {
    this.me = who;
}
parentClassFoo.prototype.identify = function() {
    return "I am " + this.me;
};

function childClassBar(who) {
    parentClassFoo.call( this, who );
}
childClassBar.prototype = Object.create( parentClassFoo.prototype );

childClassBar.prototype.speak = function() {
    alert( "Hello, " + this.identify() + "." );
};

var instanceB1 = new childClassBar( "instanceB1" );
var instanceB2 = new childClassBar( "instanceB2" );

instanceB1.speak();
instanceB2.speak();
```
Now I'll try to explain the thought process of someone coming from an object-oriented language, and the result of that approach in the code. 

OO programmers will likely want to use the `new` operator because it resembles a constructor in class-based languages, or something that creates an instance of a class. This immediately restricts the programmer in JavaScript. `new` in JavaScript is not a contructor, but rather something that creates an object and prototype links it to the constructor (the thing to the right of `new`). **The constructor _must_ be a function.** That means "classes" used with `new` must be functions, not objects.

So, the OO programmer makes `function parentClassFoo`. Since this is a function, how do we add a method to to it? Through its `[[Prototype]]` property, in `parentClassFoo.prototype.identity = function() {..}`. The same goes for `childClassBar` and `.speak`. We link the two functions' prototypes with `Object.create`. Notice here we link the _prototypes_, not the functions themselves. `Object.create(..)` must take objects, not functions as arguments.

The OO programmer than uses the `new` operator to "instantiate" `childClassBar` into instances. Remember that true instantiation does not exist in JavaScript. Here, JS is only making objects linked to the prototype of `childClassBar`.

If you're thinking "wow, this is really confusing and convoluted," then you're right. A big chunk of this [_YDKJS: this and Object prototypes_](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch6.md) is explaining why this is not good design for JavaScript, and you can see above why.

### Mental Mapping this Approach

That said, **it is important to understand what is happening**. Simpson tries to represent a mental map of this code in a couple of diagrams. I'll try to break them down. 

#### A Simple Mental Map

![simple diagram of OO-style JavaScript code](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/fig5.png)

Note that this corresponds to the first code block above; the one taken straight from the text. I'll try to explain things in order that they appeared in the code, and the relationship arrows that point away from the item of discussion.

First, we have **`Foo()`**. You can see in the legend in the function, although this is supposed to emulate a class in the code. It has a property/relationship arrow labeled `.prototype` pointing to `identify()`. This means `Foo()` has a relationship to `identify()` because `identify()` is a method that exists on `Foo.prototype`.

Let's move onto **`identify()`**. It has a relationship arrow labeled `.__proto__ [[Prototype]]` pointings towards `toString(), valueOf(), hasOwnProperty(),l isPrototypeOf()`. The relationship label is a bit confusing. It looks like `[[Prototype]]` just means the `[[Prototype]]` chain, but `.__proto__` is a bit of a separate concept. Remember that it's a getter/setter that exists on `Object.prototype`, along with `toString, valueOf()`, etc. It allows you to inspect the prototype of an object, or travel the prototype chain. So, _you could think of this relationship as just the `[[Prototype]]` chain_.

**`Bar()`** is like `Foo()`, and has the same `.prototype` relationship to the prototype method `speak()`. 

**`speak()`** has a `[[Prototype]]` relationship to `identify()` because they both exist on the `[[Prototype]]`  chain. `speak()` has an "implied relationship via `[[Prototype]]` delegation" arrow labeled "constructor" pointing to `Foo()`. 

Alright, this is confusing. The `.constructor` property _does not mean constructed by ".."_ in JavaScript. It is an arbitrary property that by default is a reference to the object property. It can be overridden. 

`speak()` exists on `Bar.prototype`. `Bar.prototype` was linked to `Foo.prototype` via `Object.create(..)`. Because the original `Bar.prototype` was overwritten with the new object, it lost it's default `.constructor` property. so it is delegated up to `Foo.prototype`, where it exists and points to `Foo`. If you ran `Bar.prototype.constructor` in the console, you would get `function Foo(who) {this.me = who;}`.  Wow, what a mental workout. Check out ["Constructor Redux"](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch5.md) in Chapter 5 for more details.

**`b1`** is an instance of `Bar()`. It has a `[[Prototype]]` relationship to speak(), meaning that `b1` delegates to `Bar.prototype`. `b1` has a dotted constructor relationpship to `Foo()` because like it is the next thing on the `[[Prototype]]` chain that has the `.constructor` property. **`b2`** is the same as `b1`.

At the top of the chart, you can see the **`Object()`** function, which is accessible by all objects via the `[[Prototype]]` chain. It has a prototype relationship to the objects `toString(), valueOf()`, etc. because those methods exist on `Object.prototype`.

#### A Complex Mental Map

Here's a more complex diagram that shows more things that are happening. Simpson says we don't have to know everything that's going on, but it's important to understand it.

![complex diagram of OO-style JavaScript code](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/fig4.png)

I'll go over additions to this diagram that are absent in the previous simpler diagram.

**`Function()`** is a function-object like **`Object()`. All functions in JavaScript can delegate to `Function()`. `Function()` has a prototype relationship to `call(), apply(), bind()` which are labeled as objects.

**`call(), apply(), bind()`** are actually methods of `Function.prototype`, so they are accessible by all functions. `call(), apply(), bind()` has a constructor relationship to `Function()` because something like the first object that has `.constructor` on the `[[Prototype]]` chain in this situation starting from `call(), apply(), bind()` is `Function()`.

Everything else is that new are relationship arrows on things that were on the first diagram.
