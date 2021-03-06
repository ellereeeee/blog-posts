![A chain link.](pictures/chain%20link.jpg)

These are my notes on "Prototypes," Kyle Simpson's [fifth chapter](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch5.md) of _You Don't Know Javascript: this and Object Prototypes_.

### `[[Prototype]]`

When attempting a property access on an object that doesn't have that property, the object's internal `[[Property]]` linkage defines where the `[[Get]]` operation should look next. For example:

```javascript
var anotherObject = {
  a: 2
};

// create an object linked to `anotherObject`
var myObject = Object.create( anotherObject );

myObject.a; // 2
```

`myObject.a` does not exist but `a` is found on `anotherObject`.

This cascading linkage from object to object essentially defines a "prototype chain" (somewhat similar to a nested scope chain) of objects to traverse for property resolution.

`for..in` will traverse the `[[Prototype]]` chain for enumerable properties and `in` will do the same regardless of enumerability.

#### `Object.prototype`

All normal object objects have the built-in `Object.prototype` as the top of the prototype chain (like the global scope in scope look-up), where the property resolution will stop if not found anywhere prior in the chain. `toString()`, `valueOf()`, and several other common utilities exist on this `Object.prototype` object, explaining how all objects in the language are able to access them.

#### Setting and Shadowing Properties

Examine the code `myObject.foo = "bar"`.

If `foo` already exists somewhere higher in the `[[Prototype]]` chain, surprising behavior can occur. If `foo` is on both `myObject` and somewhere higher on the prototype chain, the `foo` on `myObject` _shadows_ the `foo` higher in the chain. This is called _shadowing_. `myObject.foo` will find the `foo` that's lowest in the chain.

Here are three scenarios for `myObject.foo = "bar"` if `foo` is not on `myObject` but is somewhere higher on `myObject`'s `[[Prototype]]` chain:

1. If the `foo` higher in the chain is not `writable: false`, then `foo` is also added to `myObject`.

2. If the `foo` higher in the chain is `writable: false`, then `foo` is not created on `myObject`. An error is thrown in `strict mode` or nothing happens otherwise.

3. If the `foo` higher in the chain is a setter, then the setter will be called. No `foo` is added on `myObject` and the `foo` setter will not be redefined.

In cases #2 and #3 you can use `Object.defineProperty(..)` to add `foo` to `myObject`.

Number 2 is trying to reinforce the illusion of class-inherited properties if you think of the `foo` as an inherited copy to `myObject`. Such inheritance does not actually occur however.

Be careful when dealing with delegated properties that you modify. You may accidentally create shadowing. For example:

```javascript
var anotherObject = {
  a: 2
};

var myObject = Object.create( anotherObject );

anotherObject.a; // 2
myObject.a; // 2

anotherObject.hasOwnProperty( "a" ); // true
myObject.hasOwnProperty( "a" ); // false

myObject.a++; // oops, implicit shadowing!

anotherObject.a; // 2
myObject.a; // 3

myObject.hasOwnProperty( "a" ); // true
```

The `++` operation corresponds to `myObject.a = myObject.a + 1`, so `myObject.a` is created. If you want to increment `a`, `anotherObject.a++` would be proper.

### "Class"

In JavaScript, objects define their own behavior.

#### "Class" Functions

All functions by default get a public, non-enumerable property called `prototype` which points at an arbitrary object.

```javascript
function Foo() {
  // ...
}

Foo.prototype; // { }
```

Each object created from calling `new Foo()` will end up `[[Prototype]]`-linked to the "Foo dot prototype" object.

```javascript
function Foo() {
  // ...
}

var a = new Foo();

Object.getPrototypeOf( a ) === Foo.prototype; // true
```

This is something that happens with a contructor call (calling `new` with a function to create an object). Now there are **two objects linked to each other.**

In JavaScript we make _links_ between objects. For the `[[Prototype]]` mechanism, visually, the arrows move from right to left and from bottom to top.

![A prototypal relationship chart showing the relationships between objects as arrows moving right to left and from bottom to top.](pictures/visual%20prototype%20links.png)

One object can _delegate_ property/function access to another object.

#### "Constructors"

The `.prototype` object by default gets a public, non-enumerable property called `.constructor`. This property is a reference back to the function that the object is associated with. For example:

```javascript
function Foo() {
  // ...
}

Foo.prototype.constructor === Foo; /// true

var a = new Foo();
a.constructor === Foo; // true
```

`a` created by `new Foo()` _seems_ to also have a property on it called `.constructor`. However, **"constructor" does not actually mean "was constructed by."**

#### Mechanics

The `.constructor` reference is actually _delegated_ to `Foo.prototype`, which happens to have a `.constructor` that points to `Foo`.

If you create a new object and replace its default `.prototype` object reference, the new object will not get a new `.constructor` on it. For example:

```javascript
function Foo() { /* .. */ }

Foo.prototype = { /* .. */ }; // create a new prototype object

var a1 = new Foo();
a1.constructor === Foo; // false!
a1.constructor === Object; // the constructor property access is delegated to `Object.prototype`
```

`.constructor` on an object arbitrarily by default points at a function that has a reference back to the `.prototype` object. Remember: constructor does not mean constructed by.

Thus, `.constructor` is an unreliable and unsafe reference to rely upon in your code.

### "(Prototypal) Inheritance"

One way to link two objects' prototypes is with `Object.create`. For example, `Bar.prototype = Object.create( Foo.prototype )`. Now `Bar` can delegate behavior to `Foo`. This method is nice in that it doesn't have any side effects that could come from a constructor call (`new` with a function). One slight downside is that reassigning a prototype with `Object.create` throws the old prototype away.

This means the `.constructor` property is thrown away from the old `.prototype`, so you woud have to manually "fix" it if you were to rely on `.constructor`.

The ES6 `Object.setPrototypeOf(..)` utility lets you modify an existing prototype.

```javascript
// pre-ES6
// throws away default existing `Bar.prototype`
Bar.prototype = Object.create( Foo.prototype );

// ES6+
// modifies existing `Bar.prototype`
Object.setProtoypeOf( Bar.prototype, Foo.prototype );
```

#### Inspecting "Class" Relationships

Inspecting an instance for its inheritance ancestry is often called _introspection_ or _reflection_ in traditional class-oriented environments. In JS, this would be finding out what object a certain object delegates to.

One way to do this is with the `instanceof` operator.

```javascript
function Foo() {
  // ...
}

Foo.prototype.blah = "...";

var a = new Foo();

a instanceof Foo; // true
```

`instanceof` takes a plain object as its left-hand operand and a function as its right-hand operand. `instanceof` searches the entire `[[Prototype]]` chain of the object on the left to see if the prototype of the function object on the right ever appears.

`.isPrototypeOf` does the same, but lets you test the existence of a prototype on another object (not a function object).

```javascript
Foo.prototype.isPrototypeOf( a ); // true
```

You can also directly retrieve the `[[Prototype]]` of an object. In ES6 this is done with `Object.getPrototype(..)`.

```javascript
Object.getPrototypeOf( a ); // Object { blah: "...", … }

Object.getPrototypeOf( a ) === Foo.prototype; // true
```

You can also use `.__proto__`, which was standardized in ES6.  `.__proto__` retrieves the internal `[[Prototype]]` of an object as a reference, which is helpful if you want to directly inspect (or even traverse: `.__proto__.__proto__...)` the chain. `.__proto__` looks like a property but it's more appropriate to think of it as a getter/setter that exists on `Object`.

**Generally you should not change the `[[Prototype]]` of an existing object.** It's best to treat object `[[Prototype]]` linkage as a read-only characteristic for ease of reading your code later.

### Object Links

The series of `[[Prototype]]` links between objects is called the "prototype chain."

#### Links As Fallbacks?

Designing software where you intend for a developer to call `myObject.cool()` when the `cool()` method is not on `myObject` can be suprising to future developers who will maintain your software. You can design your software in less surprising way that still takes of advantage of `[[Prototype]]` linkage. For example:

```javascript
var anotherObject = {
  cool: function() {
    console.log( "cool!" );
  }
};

var myObject = Object.create( anotherObject );

myObject.doCool = function() {
  this.cool(); // internal delegation!
};

myObject.doCool(); // "cool!"
```