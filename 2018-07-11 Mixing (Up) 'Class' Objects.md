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

Instances of classes are constructed by a special method of the class, usually of the same name as the class, called a _constructor_. This method's explicit job is to initialize any information (state) the instance will need. For example:
