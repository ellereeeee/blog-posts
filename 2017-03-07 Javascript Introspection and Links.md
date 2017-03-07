Yesterday I finished up Ch. 5: Prototypes of [_YDKJS: this and Object prototypes_](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch5.md). 

This is a bit out of context, but at the end of the chapter Simpson does such a great job describing prototypal inheritance that I'm going quote and bold his words below:

> **...the [[Prototype]] mechanism is an internal link that exists on one object which references some other object.**
> 
> **This linkage is (primarily) exercised when a property/method reference is made against the first object, and no such property/method exists. In that case, the [[Prototype]] linkage tells the engine to look for the property/method on the linked-to object. In turn, if that object cannot fulfill the look-up, its [[Prototype]] is followed, and so on. This series of links between objects forms what is called the "prototype chain".**

Alright, last time I left off on the section "Inspecting Class Relationships."

Simpson explains how to check what object another object is linked to on the `[[Prototype]]` chain. In class-based languages, this would be like checking an instance for its inheritance ancestry, often called _introspection_ or _reflection_.

To check if one object exists on the prototype chain of another object, the "cleanest" way to do it is something like this: `Foo.prototype.isPrototypeOf(a); // true`. Here, Foo exists higher on the `[[Prototype]]` chain in regards to `a`. You could also compare two objects, such as `b.isPrototypeOf(c);`. This checks if `b` ever appears in `c`'s `[[Prototype]]` chain.

The `instanceof` operator can also check for `[[Prototype]]` linkage, but it only works between an object and a function. For example, `a instanceof Foo; // true`.

If you wanted to get the `[[Prototype]]` of an object, you could do something like this `Object.getPrototypeOf(a); // contructor: Foo() __proto__: Object`.

You could also use the interestingly named "_dunder proto_," or `.__proto__`, which would return the same result as `.getPrototypeOf(..)`. `.__proto__` exists at the top of the `[[Prototype]]` chain on `Object.prototype`, along with `.toString()` and `.isPrototypeOf(..)`. It is more appropriate to think of it as a getter/setter rather than a property. Check out the text for more details.

Simpson then moves on to discuss about how to create links. Previously, he talked about how many developers make `[[Prototype]]` linkages by trying to emulate classes. This can be really confusing.

`Object.create(..)` is the simplest way to create an object and link it to another object's `[[Prototype]]`. Here's an example from the text:

```
var foo = {
    something: function() {
        console.log( "Tell me something good..." );
    }
};

var bar = Object.create( foo );

bar.something(); // Tell me something good...
```

This object can only be partially polyfilled to pre-ES5 environments; using property descriptors like `enumerable` cannot be used in the `Object.create(..)` polyfill.

Simpson ends the chapter by briefly discussing how the `[[Prototype]]` linkage should be implemented in your code. He says it's easier to read code by using internal implementations of properties or methods that exist higher on the `[[Prototype]]` chain. This can be achieved by using implicit `this` bindings, such as

```
myObject.doCool = function() {
    this.cool(); // internal delegation to cool function higher on the prototype chain
};
```

Wow! This was an incredibly _dense_ chapter, but also very enlightening. I remember checking Javascript docs at mozilla seeing "something something prototype" all the time, now I actually know what they're talking about. Also, I'm really glad I'm making these reflections. They've  been really helpful in reinforcing what I've learned. There's no way I would get the concepts of `this` and prototypes I just read the book and walked way. I recommend you do the same if you're not already.