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

### Mutual Delegation (Disallowed)

