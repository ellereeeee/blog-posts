In the second half of [Ch. 5 - YDKJS: _this_ and Object prototypes](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch5.md), Simpson discusses how the `constructor` object misleads programmers into thinking it actually constructs objects.

For example, `Foo.prototype.constructor` is simply a reference to `Foo`. It did not "construct" Foo. This object is also not immutable; it can be overwritten. Therefore, it should not be relied upon.

Simpson also discusses code patterns for emulating class inheritance in Javascript via the `[[Prototype]]` chain.

The way he recommends is with the `Object.create` function. Check out his example and explanation: 

```
Bar.prototype = Object.create(Foo.prototype);
```

> The important part is `Bar.prototype = Object.create(Foo.prototype)`. `Object.create(..)` creates a "new" object out of thin air, and links that new object's internal `[[Prototype]]` to the object you specify (`Foo.prototype` in this case).

Compare this with 

```
Bar.prototype = Foo.prototype;
```

In this situation a link is created, **but a new object is _not_ created**; just a reference to `Foo.prototype`. Because of the linkage **and** lack of a new object, if you modify `Bar.prototype`, you also modify `Foo.prototype`, likely unintentionally changing the behavior of all objects linked to `Foo.prototype`.

`Bar.prototype = new Foo();` is no good because code the in `Foo()` is executed at the time of this assignment, some of which may be unwanted. Therefore, it's recommended to use `Object.create(..)`.