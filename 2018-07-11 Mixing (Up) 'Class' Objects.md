![Set squares and a protractor on an architectural drawing.](pictures/architect%20drawing.jpg)

These are my notes on "Mixing (Up) "Class" Objects," Kyle Simpson's [fourth chapter](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch4.md) of _You Don't Know Javascript: this and Object Prototypes_.

### Class Theory

OO or class oriented programming stresses that data intrinsically has associated behavior that operates on it, so  proper design is to package up the data and the behavior together. This is sometimes called "data structures" in computer science.

For example, a series of characters is usually called a "string." The characters are the data. You usually want to do operations on the string like checking its length, appending data, etc. A `String` class would include these as methods as well as the string data.

Classes imply a way of _classifying_ a certain data structure. We do this by thinking of any given structure as a specific variation of a more general base definition.

For example, we could make a `Vehicle` class that has an engine and moves people and further specify that into a `Car` class that has four wheels. `Car` would "inherit" or "extend" the behavior from the `Vehicle` class (copying it) and then further specialize the behavior (such as adding 4 wheels).

The above classes define behavior. An actual `Car` object would be made by instantiation. An instance of `Car` would be like a car with a unique vehicle identification number.

**Classes inherit behavior from other classes and are made into objects through instantiation.**

Another important concept is "polymorphism." [Polymorphism](https://whatis.techtarget.com/definition/polymorphism) is the characteristic of being able to assign different meaning or usage to something in different contexts. 

We will refer to it as the idea that the general behavior from a parent class can be overridden in a child class to give it more specifics. Class theory suggests parent and child classes should have the same name for certain behaviors, so the child overrides the parent.

**JavaScript does not have classes.**

Classes are an optional design pattern and you have the choice to use them in JavaScript.

### Class Mechanics

A class is a blue-print. To get an object to interact with, we must build (aka instantiate) something from the class. The end result of the "construction" is an object, usually called an "instance," which we can access via methods. 

The object is a copy of all the characteristics described by the class. A class is instantiated into an object by a copy operation.

![A simple class example.](pictures/simple%20class%20example.png)

In the chart above, you can see `a1` and `a2` instantiated from class `Foo`. The `Bar` class is inherited from `Foo` and `b1` and `b2` are instantiated from `Bar`.

Instances of classes are constructed by a special method of the class, usually of the same name as the class, called a _constructor_. This method's explicit job is to initialize any information (state) the instance will need. For example, checkout this pseudo-code:

```javascript
class CoolGuy {
  specialTrick = nothing

  CoolGuy( trick ) {
    specialTrick = trick
  }

  showOff() {
    output( "Here's my trick: ", specialTrick )
  }
}
```

To make a `CoolGuy` instance we call the class constructor:

```javascript
Joe = new CoolGuy( "jumping rope" )

Joe.showOff() // Here's my trick: jumping rope
```

The constructor of a class belongs to the class.

### Class Inheritance

You can define another class that inherits from the first class.

The second class is often said to be a "child class" whereas the first is the "parent class".

The child class contains an initial copy of the behavior from the parent, but can then override any inherited behavior and even define new behavior. For example:

```javascript
class Vehicle {
  engines = 1

  ignition() {
    output( "Turning on my engine." )
  }

  drive() {
    ignition()
    output( "Steering and moving forward!" )
  }
}

class Car inherits Vehicle {
  wheels = 4
  
  drive() {
    inherited:drive()
    output( "Rolling on all ", wheels, " wheels!" )
  }
}
```

#### Polymorphism

`Car` defines its own `drive()` method, which overrides the method of the same name it inherited from `Vehicle`. But car also references `inherited:drive()`, which means that `Car` can reference the original pre-overriden `drive()` it inherited.

This is polymorphism, or the idea that any method can reference another method at a higher level of the inheritance hierarchy. It is important to note that a child class is actually accessing behavior from the copy it received from the parent class. The child class does not access the parent class. **Class inheritance implies copies**.

We refer to this as "relative" polymorphism because we only try to access a reference from one level up the inheritance hierarchy.

In many languages the keyword `super` is used instead of `inherited:`, which leans on the idea that a "super class" is the parent/ancestor of the current class. `super` is a way for the constructor of a child class to relatively reference the constructor of its parent class via inherited behavior.

Through polymorphism, method definitions can change depending on what class you reference an instance of.

#### Multiple Inheritance

Some class-oriented languages allow you to inherit from more than one parent. Multiple inheritance means each parent class definition is copied in to the child class. 

Multiple inheritance introduces several issues. The "Diamond Problem" refers to a scenario where class "D" inherits from "B" and C", and each of those inherits from "A". If "B" and "C" both have their own distinct `drive()` methods, which one should "D" inherit?

JavaScript does not have this functionality, although some have tried to introduce this code pattern.

### Mixins

JavaScript does not have classes, just objects. They do not get copied, but linked together.

JS developers fake the missing copy behavior of classes through mixins.

The name "mixin" comes from the idea that you are "mixing in" the contents of a parent "class" (object in JS) into a child "class".

#### Explicit Mixins

The utility for copying behavior from a parent class to child class is often called `extend(..)`. Here we will call them `mixin(..)` for illustrative purposes.

An explicit mixin function will take an source and target objects for parameters. It will check if the target project has the properties of the source object and if it doesnt it will "copy" it via `=` assignment.

Polymorphism is achieved by calling the desired parent method in the context of the of the child class via `.call(this);`. For example:

```javascript
var Car = mixin( Vehicle, {
  wheels: 4,
  
  drive: function() {
    Vehicle.drive.call( this );
    console.log( "Rolling on all " + this.wheels + " wheels!" );
  }
} );
```

Kyle Simpson calls `Vehicle.drive.call( this )` an example of "explicit pseudo-polymorphism".

This style of code usually results in more complex, harder-to-read, and harder-to-maintain code because you have to create this manual and explicit linkage in every function that needs a polymorphic reference.

Mixins are not the same as real duplication in classes because mixins only copy references.

#### Implicit Mixins

Implicit mixins are when you just "borrow" the function of another object. They do not use a function to copy properties like explicit mixins. For example:

```javascript
var Another = {
  cool: function() {
    // borrow Something's cool function
    Something.cool.call( this );
  }
};
```

The style of code has the same issues as explicit mixins in regards to its manual and explicit linkage making for brittle code.

Faking classes in JS can present many problems.