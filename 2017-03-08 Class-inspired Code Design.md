# Class-inspired Code Design

Let me start off by saying this: **class-inspired code design in JavaScript is convoluted and confusing. Don't do it!** I'll explain why soon.

In this post I'm going to try to explain the thought process of someone from a class-based oriented language (like Python or Ruby) trying to implement a class-inspired approach with JavaScript's `[[Prototype]]` chain. No, **I don't actually come from from a class-based language; JavaScript is my first. This is a mental exercise for myself.**

### Classical (Prototypal) Object Oriented Code Design

I'll be using and explaining examples from Chapter 6: Behavior Delegation of [_YDKJS - this and Object prototypes_](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch6.md).

Here is an implementation of what Simpson calls a "_classical (prototypal) object oriented style._"

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

If you're thinking "wow, this is really confusing and convoluted," then you're right. A big chunk of this [_YDKJS: this and Object prototypes_](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch6.md) is explaining why this is not good design for JavaScript, and you can see why above.

### Mental Mapping this Approach

That said, **it is important to understand what is happening**. Simpson tries to represent a mental map of this code in a couple of diagrams. I'll try to break them down. 

#### A Simple Mental Map

![simple diagram of OO-style JavaScript code](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/fig5.png)

Note that this corresponds to the first code block above; the one taken straight from the text. I'll try to explain things in order that they appeared in the code, and the relationship arrows that point away from the item of discussion.

First, we have **`Foo()`**. You can see in the legend that it's a function, although this is supposed to emulate a class in the code. It has a property/relationship arrow labeled `.prototype` pointing to `identify()`. This means `Foo()` has a relationship to `identify()` because `identify()` is a method that exists on `Foo.prototype`.

Let's move onto **`identify()`**. It has a relationship arrow labeled `.__proto__ [[Prototype]]` pointings towards `toString(), valueOf(), hasOwnProperty(),l isPrototypeOf()`. The relationship label is a bit confusing. It looks like `[[Prototype]]` just means the `[[Prototype]]` chain, but `.__proto__` is a bit of a separate concept. Remember that it's a getter/setter that exists on `Object.prototype`, along with `toString, valueOf()`, etc. It allows you to inspect the prototype of an object, or travel the prototype chain. So, _you could think of this relationship as just the `[[Prototype]]` chain_.

**`Bar()`** is like `Foo()`, and has the same `.prototype` relationship to the prototype method `speak()`. 

**`speak()`** has a `[[Prototype]]` relationship to `identify()` because they both exist on the `[[Prototype]]`  chain. `speak()` has an "implied relationship via `[[Prototype]]` delegation" arrow labeled "constructor" pointing to `Foo()`. 

Alright, this is confusing. The `.constructor` property _does not mean constructed by ".."_ in JavaScript. It is an arbitrary property that by default is a reference to the object property. It can be overridden. 

`speak()` exists on `Bar.prototype`. `Bar.prototype` was linked to `Foo.prototype` via `Object.create(..)`. Because the original `Bar.prototype` was overwritten with the new object, it lost it's default `.constructor` property. So `Bar.prototype.constructor`(which _would_ exist in the same place as `speak()`) is delegated up to `Foo.prototype`, where `.constructor` exists and points to `Foo`. If you ran `Bar.prototype.constructor` in the console, you would get `function Foo(who) {this.me = who;}`.  Wow, what a mental workout. Check out ["Constructor Redux"](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch5.md) in Chapter 5 for more details.

**`b1`** is an instance of `Bar()`. It has a `[[Prototype]]` relationship to speak(), meaning that `b1` delegates to `Bar.prototype`. `b1` has a dotted constructor relationpship to `Foo()` because like it is the next thing on the `[[Prototype]]` chain that has the `.constructor` property. **`b2`** is the same as `b1`.

At the top of the chart, you can see the **`Object()`** function, which is accessible by all objects via the `[[Prototype]]` chain. It has a prototype relationship to the objects `toString(), valueOf()`, etc. because those methods exist on `Object.prototype`.

#### A Complex Mental Map

Here's a more complex diagram that shows more things that are happening. Simpson says we don't have to know everything that's going on, but it's important to understand it.

![complex diagram of OO-style JavaScript code](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/fig4.png)

I'll go over additions to this diagram that are absent in the previous simpler diagram.

**`Function()`** is the `global Function` object. Taken from [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function):

>The global Function object has no methods or properties of its own, however, since it is a function itself it does inherit some methods and properties through the prototype chain from Function.prototype.

**`Function()`** has a prototype relationship to `call(), apply(), bind()` which are labeled as functions. `Function()` also has a `[[Prototype]]` relationship. I'm going to make a note on this later; I don't understand what's happening here.

**`call(), apply(), bind()`** are methods of `Function.prototype` that all functions in JavaScript can delegate to. `call(), apply(), bind()` has a constructor relationship to `Function()` because the first object that has `.constructor` on the `[[Prototype]]` chain in this situation starting from `call(), apply(), bind()` is `Function()`. `call(), apply(), bind()` has a `[[Prototype]]` relationship to `toString(), valueOf()` etc. because the `global Function` object is an object, so that means it  can delegate to the methods of `Object.prototype`.

Everything else that is new are relationship arrows on things that were on the first diagram.

Both `Foo()` and `Bar()` have a `[[Prototype]]` relationship to `call(), apply(), bind()` because all functions can delegate to the `global Function` object. `Foo()` and `Bar()` also have a `.constructor` relationship to `Function()`. Since neither `Foo()` nor `Bar()` have the `.constructor` property, it delegates this property to the `global Function` object. _Do not confuse `Foo()` with `Foo.prototype`. They are not the same objects!!!_ `Foo.constructor` refers to the `global Function` object, while `Foo.prototype.constructor` refers to `Foo()`.

The global function object `Object()` also has a constructor relationship to the `global Function` object. `Object()` also has a `.__proto__` relationship to `call(), apply(), bind()` because `Object()` is a function object; therefore, it delegates to these behaviors. 

**Note:** The relationship between `Object()` and `Function()` seems to be cyclic since their `[[Prototype]]` chain loops through each other. They are both global function-objects.

### The Takeaway and Some Thoughts

This is what you should takeaway from this:

1) Using class-inspired code design is confusing and convoluted

2) Although the internal mechanisms of JavaScript are complex, they are consistent and thus comprehendable.

In my next post, I'm going to go over a code design approach that is much more sensible and simpler given JavaScript's prototype-based nature.

Wow, this was one tough mental exercise. The book actually shows the more complex diagram first, and to be honest it was really intimidating. Trying to explain everything that's happening definitely helped my understanding. That said, there's stll one thing I don't understand: why is there a `.__proto__ / [[Prototype]]` relationship going from the `global Function` object to its methods like `call(), apply(), bind()` on the complex diagram? Up until now I thought that relationship represented the path/direction of the `[[Protoype]]` chain, which exists on the `.prototype` property. I'm not sure now. I asked for some help on the [Freecodecamp forums](https://forum.freecodecamp.com/t/question-on-the-global-function-object-and-the-prototype-chain/95299) and I'll update this once I get an answer.

**edit:** I got a good answer from the FCC forums that explains my confusion above. All funcions can delegate to the `global Function` methods `call(), apply(), bind()`. That's why there is a `.__proto__ / [[Prototype]]` relationship arrow from `Function()` to `call(), apply(), bind().` It's sort of recursive in this aspct.