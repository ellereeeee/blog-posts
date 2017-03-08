Yesterday I got through some of Chapter 6: Behavior Delegation in [_YDKJS - this and Object prototypes_](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch6.md).

Simpson discusses again the differences between class-based and prototype-based (or _delegation_ based) design. He starts out by saying **using class-based design in JavaScript is counter-intuitive because it is at odds with the prototype based nature of the language**.

> In JavaScript, the [[Prototype]] mechanism links objects to other objects. There are no abstract mechanisms like "classes", no matter how much you try to convince yourself otherwise. **It's like paddling a canoe upstream: you can do it, but you're choosing to go against the natural current, so it's obviously going to be harder to get where you're going.**

This is without a doubt the main message of this book.

This chapter mainly focuses on how to properly implement delegation behavior (the prototype chain) into your code. Here are some things to keep in mind when implementing delegation:

1) Keep the state on the delegators. This mean that if an object needs to go up the prototype chain to delegate a behavior, the state remains on the current object via the context object (implicit `this` binding). For example:

```javascript
XYZ.prepareTask = function(ID,Label) {
    this.setID( ID );
    this.label = Label;
};
```

`this.setID(ID);` exists on an object higher on the prototype chain, but the state remains on XYZ because `this` refers to the context object.

2) Name your object methods to be specifically descriptive of the behavior you want the methods to achieve. This helps avoid _shadowing_, which is generally not good design because shadowing calls for awkward syntax via explicit pseudo-polymorphism. Descriptive method names are also helpful because they are self-documenting and make code more maintainable.

3) Use implicit `this` bindings to delegate behavior to objects higher on the prototype chain. I think this point is closely related to the first point. 

This is important: Simpsons says that delegation is properly used when it is not exposed in our code. In other words, instead of expecting people who use or code to explicitly use the prototype chain, we "hide" prototype linkage in methods (via implicit `this` bindings) of objects. **The developers leverage delegation to happen behind the scenes.**

Simpson explains how JavaScript does not allow mutual delegation in order to achieve performance optimizations. He also talks about how the `constructor` objects can be used in Google Chrome developer tools to see the constructor (function object) when you use the `new` operator. However, it's important to note that this is only really helpful if you use a class-based design pattern in your JavaScript code.

In my next post, I'll try to dissect and explain a class-inspired implementation of the prototype chain.

