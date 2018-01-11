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

Since there is no global variable "name," nothing is returned.

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