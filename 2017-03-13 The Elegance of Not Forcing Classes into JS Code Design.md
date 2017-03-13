# The Elegance of Not Forcing "Classes" into JS Code Design

I finally finished [_YDKJS: this and Object prototypes_](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch6.md). In this post I'll briefly go over why writing code that leverages the [[Prototype]] mechanism is simpler than using class-inspired code. I'll also summarize the rest of Ch. 6 and Appendix A.

Check out this code from [Ch. 6: Behavior Delegation](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch6.md)

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
    alert( "Hello, " + this.identify() + "." );
};

var b1 = Object.create( Bar );
b1.init( "b1" );
var b2 = Object.create( Bar );
b2.init( "b2" );

b1.speak();
b2.speak();
```

Here we simply have objects. No "classes" represented as function-objects, no manipulating `.prototype` objects, no worrying about what the `.constructor` object refers too, and no messing with the `new` operator.

This is simple code that leverages the `[[Prototype]]` mechanism.

Here is a mental map of the code:

![a diagram of OLOO-style code](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/fig6.png). Compare this with [class-inspired diagram](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/fig4.png)

One of my friends (not studying code) saw it and asked me if I was studying electrical engineering...

Simpson spends a good portion of Ch. 6 showing in plausible real life examples of code where OLOO (objects linked to other objects) style code would be better than classical OO(object-oriented, or class-inspired) style code. In one example, he cleverly shows that using OO style code would require three "classes," but OLOO style code would only require two objects.

Simpson then discusses some new syntax in ES6. He discusses how the syntax `class` seems to make using OO style code look better. Although it does help this style of code in some respects, there are also some bad tradeoffs. He explains in [Appendix A](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/apA.md) how `super`(the syntax for JavaScripts pseudo-polymorphism) is not dynamic like `this`. Therefore, it requires you to make your code a bit more static in order to properly leverage `super`. His main point about `class`, and OO style-coding in general, is that it still makes JavaScript code design more complicated by putting restrictions on your code to equate to your mental map of code design, which is at odds with the simple and elegant nature of JavaScript's `[[Prototype]]` mechanism.

#### Thoughts and What's Next
I found this to be tougher than Simpson's second book on Scope and Closures, and in some instances a tad frustrating. Not because the subject matter itself is new to me, but there are some instances where Simpson brought up a deep concept or not simple piece of knowledge in his explanations. For example, I didn't like how he brought up the global Function constructor and expected the reader how it's `[[Prototype]]` chain is unique, or how he brought up composition in one of his other examples. That said, these books are _free_ after all, and I also learned a lot, so I'm really grateful.

I'll be re-making my portfolio website next. I'm pretty excited; my main reason for diverting from FCC's curriculum was because I was frustrated that I didn't know how to use Git and how to code locally. Now that I can do both! It will be nice to apply what I learned in Shay Howe's HTML and CSS tutorial as well as Udacity's course on How to Use Git and Github.

If you're interested in what web-development guide I'm following, you can see it here on a [trello board](https://trello.com/b/S9lZOJ7w/template-webdev-cs-intro-training). The guide was made by FCC user P1xt and the trello board was made by FCC user MARKJ78. I was previousy following it out of order due to some indeciveness, but I'm on track now.