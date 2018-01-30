![React logo](pictures/React logo.png)

This is the sixth part of my notes on egghead.io's [The Beginner's Guide to ReactJS](https://egghead.io/courses/the-beginner-s-guide-to-reactjs).

### Use Class Components with React

This section will show how implicit `this` bindings are lost when updating state in React and how to deal with it in different ways. The title of the video would be better named "Setting State and `this`".

#### Implicit Binding and Losing It

The `this` binding is determined by these rules in order of precedence:

1. If `new` is called, `this` is bound to the newly constructed object.

2. If `call` or `apply` (or `bind`) is called, `this` is bound to the specified object.

3. If a context object owning the call is called, `this` is bound to the context object.

4. `this` by default is `undefined` in strict mode or a global object otherwise. 

We will focus on number 3, which is called **implicit binding**.

This is a vanilla JavaScript example of an implicit binding and the implicit binding being lost.

```
var name = "GLOBAL OBJECT NAME";

function greet (name) {
  return `Hi ${name}, my name is ${this.name}!`
}

var bob = {
  name: 'Bob',
  greet: greet
}

bob.greet('Jane'); // Hi Jane, my name is Bob!

var greetFn = bob.greet; // this binding is lost!

greetFn('Jane'); // // Hi Bob, my name is GLOBAL OBJECT NAME!
```

Although `bob` doesn't "own" the function `greet`, you could say it does at the time `bob.greet` is called. In this situation, we say `bob` is the context object and `this` is "implicitly" bound to `bob`.

The `this` binding to `bob` is lost when `bob.greet` is assigned to the plain function `greetFn` because `greetFn` is a plain function that references `greet`. The default binding is applied and `GLOBAL OBJECT NAME` is logged to the console.

It's important to note that even though it `greetFn` looks like a reference to `bob.greet`, it's actually just a reference to the function `greet`.

Let's look at something similar:

```
var bob = {
  name: 'Bob',
  greet(name) {
    return `Hi ${name}, my name is ${this.name}`;
  }
}

bob.greet('Jane'); // "Hi Jane, my name is Bob"

var greetFn = bob.greet;

greetFn('Jane'); // "Hi Jane, my name is !"
```

Since there is no global variable `name`, nothing is returned.

Now we can understand how something similar happens when updating state in React, and how to deal with it.

#### Losing `this` Binding in Class Components

We'll start by making a button that increments a number when it's clicked.

```
class Counter extends React.Component {
  constructor(...args) {
    super(...args)
    this.state = {count: 0}
  }
  render() {
    return (
      <button 
        onClick={() =>
          this.setState(({count}) => ({
            count: count + 1,
        }))}
      >
        {this.state.count}
      </button>
    )
  }
}
ReactDOM.render(
  <Counter />,
  document.getElementById('root'),
)
```

![The initial incrementing button. It works.](gifs/initial counter.gif)

Focus on on the `onClick` value. We have an arrow function that increments state with `this.setState`.

While this code is completely functional, we could assign the `this.setState` function to an event handler for more readability.

The edited code would look like this: 

```
  handleClick() {
          this.setState(({count}) => ({
            count: count + 1,
        }))
  }
  render() {
    return (
      <button 
        onClick={this.handleClick}
      >
        {this.state.count}
      </button>
    )
  }
```

![The button does not work](gifs/broken counter.gif)

Uh oh! It doesn't work anymore. If we open the developer's console, we get `Uncaught TypeError: Cannot read property 'setState' of undefined`.

`this`'s binding in `this.setState` to the object created by class `Counter` was lost when we assigned it to `handleClick()`. We are just assigning a reference to the `setState` function to `handleClick()`. `handleClick()` is also just a plain function. Because [classes in ES6 are always run in strict mode](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes), `this` defaults to `undefined`. Notice the parallels from the example above in JavaScript.

#### Preserving `this` Binding in Class Components

We'll cover several ways to ensure the `this` binding is not lost.

One way uses "explicit binding," or rule number 2 from the above `this` binding rules. This is when we explicitly say what we want `this` to bind to with `call`, `apply`, or `bind`. 

Here we'll **use the built-in `bind` utility** in the `onClick` assignment. `bind` returns a new function that is hard-coded to call the original function with the `this` context object you specified. This variation of explicit binding involving assigning `call`, `apply`, or `bind` expressions to functions is called "hard binding."

The timer will function properly if we edit `onClick` like this:

`onClick={this.handleClick.bind(this)}`

While this works, this could be a performance bottleneck in some situations.

To deal with the bottleneck we can reference the prototypal method `handleClick()` by adding `this.handleClick` in the constructor and assigning it a pre-bound `handleClick` method.

The constructor would look like this:

```
  constructor(...args) {
    super(...args)
    this.state = {count: 0}
    this.handleClick = this.handleClick.bind(this)
  }
```

This is the same as the common pattern of lexically capturing `this` in pre-ES6 code, like in `var self = this;`.

This solution can be cumbersome if we need to do this for a lot of methods.

Let's look at another solution with [public class fields](https://tc39.github.io/proposal-class-public-fields/). In a nutshell, these let us declare class properties without the constructor.

Look at the code below for before and after public class fields are implemented.

Before:

```
  constructor(...args) {
    super(...args)
    this.state = {count: 0}
    this.handleClick = this.handleClick.bind(this)
  }
  handleClick() {
          this.setState(({count}) => ({
            count: count + 1,
        }))
  }
```

After:

```
  state = {count: 0}
  handleClick = this.handleClick.bind(this)
  handleClick() {
          this.setState(({count}) => ({
            count: count + 1,
        }))
  }
```

We've moved `this.state` and the `this.handeClick` assignments out of the constructor and into the class body. `this.` is no longer necessary since `state` and `handleClick` are in the class body. `state` and `handeClick` are the public class fields and we assign expressions to them. Since the constructor has become the same as the default constructor, we've removed it.

The incrementing counter will work with this implementation.

We could refactor this by removing the `handleClick()` method from the prototype and having it only on the instance.

```
  state = {count: 0}
  handleClick = function() {
          this.setState(({count}) => ({
            count: count + 1,
        }))
  }.bind(this)
```

While this works, we have to call the `.bind` method every time we update state. We can use the lexical `this` that are possible with arrow functions to workaround this.

```
  handleClick = () => {
          this.setState(({count}) => ({
            count: count + 1,
        }))
  }
```

#### Which way is best?

We discussed four main ways:

1) Hard binding the method with `.bind` in event handler assignment value

2) Lexically capturing the prototype method in the constructor

3) A variation of number 2 using public class fields

4) Using lexical `this` via arrow functions

The issue with number 1 is that it can be a performance bottleneck in some situations. Number 2 can be cumbersome if we have to do it for many methods. Number 3 is currently an experimental implementation; it's probably not good for production code (although it's commonly used in many projects).

Number 4 works and is very common, but it's not without its criticisms. [YDKJS](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch2.md) author Kyle Simpson says that using lexical `this` reinforces a bad practice of evading a solid understanding of `this`. He suggests people either stick with lexical style code or embrace `this` and avoid lexical `this`.

Either way it's important to understand both hard binding and lexical `this` so you can understand others' code and adapt to any situation when working in a team.

#### TL;DR

Be careful of losing the `this` binding when updating state in class components. You can keep the binding with either the `.bind` method, capturing lexical `this` via assignment in the constructor or with public class fields, or with the lexical `this` included in arrow functions.

### Manipulate the DOM with React refs

This section will explain how to manipulate the DOM node directly with React's `ref` prop. Sometimes this is necessary for some JavaScript libraries to work or when we want to get the value of form fields.

We'll use [vanilla-tilt.js](https://micku7zu.github.io/vanilla-tilt.js/) as an example on how to make a JavaScript library functional with `ref`.

#### Making a Static Image

This is our base code:

```
class Tilt extends React.Component {
  render() {
    return (
      <div className="tilt-root">
       <div className="tilt-child">
         <div {...this.props} />
       </div>
      </div>
    )
  }
}

const element = (
  <div className="totally-centered">
    <Tilt>
      <div className="totally-centered">
        vanilla-tilt.js
      </div>
    </Tilt>
  </div>
)

ReactDOM.render(
  element,
  document.getElementById('root'),
)
```

Class `Tilt` renders a div with the class `tilt-root`, which nests a div with the class `tilt-child`, which nests a div that spreads the props. `tilt-root` styles one div to have the colored gradient while `tilt-child` is a smaller white box. Both also have styles for animations we'll see later.

We then create constant `element`, which is the `Tilt` component wrapped in a div with the class `totally-centered`. There is another div with the same class nested inside of `Tilt` with the words "vanilla-tilt.js". `totally-centered` are flexbox styles that horizontally and vertically center content.

This code produces this static image:

![Static white box in a colored gradient box.](pictures/static vanilla-tilt.png).

#### Adding `ref`

Taken from the [React docs](https://reactjs.org/docs/refs-and-the-dom.html),

> The ref attribute takes a callback function, and the callback will be executed immediately after the component is mounted or unmounted.
>
> When the ref attribute is used on an HTML element, the ref callback receives the underlying DOM element as its argument.

In our code, we'll use the `ref` callback to store a reference to our desired DOM node.

The render method now returns this:

```
    return (
      <div
       ref={node => (this.rootNode = node)}
       className="tilt-root">
       <div className="tilt-child">
         <div {...this.props} />
       </div>
      </div>
```

It's a good practice not to assign arrow functions to refs since arrow function assignments are recreated on each rerender and for [other reasons](https://stackoverflow.com/questions/36677733/why-shouldnt-jsx-props-use-arrow-functions-or-bind).

We could rewrite the code like this:

```
  setRef = node => {
    return this.rootNode = node
  }
    render() {
    return (
      <div
       ref={this.setRef}
```

We can see we're accessing the node we want with:

```
  componentDidMount() {
    console.log(this.rootNode)
  }
```

![logging this.rootNode in the console](pictures/rootNode.png)

#### Using the Node with a Library

Now we can use `this.rootNode` with the vanilla-tilt library.

We bring in the vanilla-tilt library with a script `<script src="https://unpkg.com/vanilla-tilt@1.4.1/dist/vanilla-tilt.min.js"></script>`.

Then we can use the vanilla-tilt library by putting the `VanillaTilt` global in the `ComponentDidMount` method because we can access the node after the component mounts. We initialize `VanillaTilt` with `init` then pass in some options onto the node (`this.rootNode`). Our new code looks like this:

```
  componentDidMount() {
    VanillaTilt.init(this.rootNode,{
      max: 25,
      speed: 400,
      glare: true,
      'max-glare': 0.5,
    })
  }
```

![VanillaTilt working.](gifs/VanillaTilt.gif)

It works!

#### TL;DR

To manipulate the DOM,

1) Pass on a `ref` on the element that you're rendering. Putting `ref` on a class references an instance of that class.

2) In the `ref` value, pass in the node as the argument and return an assignment of the node to a value in the instance (ex: `this.rootNode`).

3) After the component is mounted, the node can be used for a library or something else.