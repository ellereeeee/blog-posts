![OLOO style code in the editor.](pictures/OLOO%20style%20code.png)

These are my notes on "Behavior Delegation," Kyle Simpson's [sixth chapter](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch6.md) of _You Don't Know Javascript: this and Object Prototypes_.

## Toward Delegation-Oriented Design

The `[[Prototype]]` mechanism represents a different design pattern from classes.

### Class Theory

Let's say we have similar tasks that we need to model in our code.

With classes, you would define a general parent (base) class like `Task` and define shared behavior for all the similar tasks. Then you define child classes which both inherit from `Task`, each with other own specialized behavior to handle their respective tasks.

Class design pattern encourages to use method overriding (polymorphism) to override the definition of an inherited method. This could include using `super` to call the inherited/base version of the method then adding more behavior to it. Here is an example in pseudo-code (not JS!):

```javascript
class Task {
  id;
  
  // constructor `Task()`
  Task(ID) { id = ID; }
  outputTask() { output( id ); }
}

class XYZ inherits Task {
  label:
  
  //constructor `XYZ()`
  XYZ(ID,Label) { super( ID ); label = Label; }
  outputTask() { super(); output( label ); }
}

class ABC inherits Task {
  // ...
}
```

You can instantiate one or more copies of the `XYZ` child class and use those instance(s) to perform task "XYZ". These instances have copies of both the general `Task` defined behavior and the specific `XYZ` defined behavior.

### Delegation Theory

Let's try to solve the same task (having things that do general and specialized behaviors) using behavior delegation in JS.

First you define an object (neither a class nor a function) called `Task` that has concrete behavior which includes that various other tasks can delegate to. For each specific task ("XYZ", "ABC"), you define an object to hold that task-specific data/behavior. You link your task-specific objects to the `Task` utility object, allowing them to delegate to it when necessary. For example:

```javascript
var Task = {
  setID: function(ID) { this.id = ID; },
  outputID: function() { console.log( this.id ); }
};

// make `XYZ` delegate to `Task`
var XYZ = Object.create( Task );

XYZ.prepareTask = function(ID,label) {
  this.setID( ID );
  this.label = Label;
};

XYZ.outputTaskDetails = function() {
  this.outputID();
  console.log( this.label );
};

// = Object.create( Task );
// ABC ... = ...
```

`XYZ` is set up via `Object.create(..)` to `[[Prototype]]` delegate to the `Task` object. Kyle Simpson calls thies style of code "OLOO" (objects-linked-to-other-objects).

Here are some other diffrences to note with OLOO style code:

1. With `[[Prototype]]` delegation involved, you want state to be on the delegators (`XYZ`, `ABC`), not on the delegate (`Task`).

2. Avoid naming things the same (overriding) at different levels of the `[[Prototype]]` chain because those name collisions create awkward/brittle syntax (like explicit `this` bindings). Use descriptive behavior method names so your code is self documenting.

3. Using implicit call-site `this` binding rules at `this.setID(ID);` means the `this` binding for the function call is `XYZ` even though the method is found on `Task`. In other words, `XYZ` can delegate behavior to `Task`.

**Behavior Delegation** means let some object (`XYZ`) provide a delegation (to `Task`) for property or method references if not found on the object (`XYZ`).

Rather than organizing objects in your mind vertically, with Parents flowing down to Children, think of object side-by-side as peers, with any direction of delegation links between the objects as necessary.

Delegation is more properly used as an internal implementation detail rather than exposed in your API design. For example, we hid the `.setID` delegation above in `XYZ.prepare(task)` rather than making the user call `XYZ.setID()`.

#### Mutual Delegation (Disallowed)

You cannot create a cycle were two or more objects are mutually delegated (bi-directionally) to each other. If you make `B` linked to `A`, and then try to link `A` to `B`, you will get an error. If you made a reference to a property/method which didn't exist in either place, you'd have an infinite recursion on the `[[Prototype]]` loop.

#### Debugged

At the time of me typing these notes, Chrome actively tracks the name of the function that did the construction while others browsers don't.

```javascript
function Foo() {}

var a1 = new Foo();

a1; // this logs `Foo {}` in Chrome, but `Object {  }` in Firefox
```

If you don't use a "constructor" (`new` with a function) to make your objects, such objects in Chrome will be outputted at `Object{}`, meaning "object generated from Object() construction".

"Constructor name" tracking is really only useful if you're fully embracing "class-style" coding, but not if you're using OLOO delegation.

### Mental Models Compared

This snippet uses the classical ("prototypal") OO style:

```javascript
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

b1.speak;
b2.speak;
```

Parent class `Foo` inherits to child class `Bar` which is instantiated as `b1` and `b2`. `b1` and `b2` delegate to  `Bar.prototype` which delegates to `Foo.prototype`.

Here is the same functionality but with OLOO style code:

```javascript
var Foo = {
  init: function(who) {
    this.me = who;
  },
  identify: function() {
  return "I am " + this.me;
  }
};

var Bar = Object.create( Foo );

Bar.speak = function() {
  alert( "Hello, " + this.identify() + ".");
};

var b1 = Object.create( Bar );
b1.init( "b1" );
var b2 = Object.create( Bar );
b2.init( "b2" );

b1.speak();
b2.speak();
```

The main takeaway from this code comparison is that we've greatly simplified our code by just using objects linked to each other. There is no need to use things like look (but don't behave) like classes, with constructors and prototypes and `new` calls.

Let's examine mental models of the above code snippets.

![A mental model of OO style code.](pictures/OO%20style%20mental%20map.png)

This model just shows the relevant entities and relationships.

Note how the dotted lines are depicting the implied relationships when you setup the "inheritance" between `Foo.prototype` and `Bar.prototype` and haven't yet fixed the missing `.constructor` property reference. Remember an object loses its own `.contsructor` property when you redefine the objects' prototype. Even without these dotted lines this model is still a lot to deal with.

Compare this with a mental model for OLOO-style code.

![A mental model of OLOO style code.](pictures/OLOO%20style%20mental%20map.png)

From a glance it's obvious that OLOO_style code has much less stuff to worry about, which results from just having objects linked to other objects.

### Classes vs. Objects

Let's compare differences between these two styles of coding by imagining coding UI widgets.

### Widget "Classes"

Here's how we would implement "class" design in JS without any special "class" syntax.  We'll use jQuery for DOM and CSS manipulation for our code snippets, which is not an important detail for the topics we're discussing. 

I tried to incorporate only notable code for the sake of brevity.

```javascript
// Parent class
function Widget(width, height) {
  // ...
}

Width.prototype.render = function($where) {
  // ...
}

// Child class
function Button(width, height, label) {
  // "super" constructor call
  Widget.call( this, width, height );
  // ...
}

// make `Button` "inherit" from `Widget`
Button.prototype = Object.create( Widget.prototype );

// override base "inherited" `render(..)`
Button.prototype.render = function($where) {
  // "super" call
  Widget.prototype.render.call( this, $where );
  // ...
}

Button.prototype.onClick = function(evt) {
  // ...
}

// instantiate the classes
$( document ).ready( function() {
  var $body = $( document.body );
  var btn1 = new Button( 125, 30, "Hello World" );
  
  btn1.render( $body );
} );
```

The main thing to note are the explicit `this` bindings to fake "super" calls, or override inherited/base methods. This makes for brittle, unelegant, and harder-to-maintain code.

Using ES6's `class` and `super(..)` syntax can make this code look better but it is important to remember that its just syntactic sugar for the `[[Prototype]]` mechanism.

### Delegation Widget Objects