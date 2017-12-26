![React logo](pictures/React logo.png)

This is the fourth part of my notes on egghead.io's [The Beginner's Guide to ReactJS](https://egghead.io/courses/the-beginner-s-guide-to-reactjs).

### Style React Components

This section will explain how to style React components and how destructuring CSS classes can make our components more flexible.

Look at `className` and `style` in the code below.

```
const element = (
  <div>
    <div 
       className = "box box--small"
       style={{paddingLeft: 20}}
    >
        box
      </div>
    
  </div>
)
```

Notice the syntactic differences with vanilla CSS. We use `className` instead of `class`. It is camel-cased instead of kebab-cased. We cannot use `class` as a variable; it would be a syntax error. `paddingLeft` is written in the same style. 

`style` takes the value of 20 (type number) instead of the string `20px`. React converts it to pixels. **Note that values are also not vendor-prefixed.** Also, `style` takes an object instead of a string. We'll see how this is useful soon. 

With these simple CSS styles:

```
  .box {
    border: 1px solid black;
  }
  
  .box--large {
    width: 240px;
    height: 240px;
  }
  
  .box--medium {
    width: 120px;
    height: 120px;
  }
  
  .box--small {
    width: 60px;
    height: 60px;
  }
```

We get this result:

![A plain white box with a thin black border with the word "box" in the middle..](pictures/plain box.png)

Here we make the React component `Box` that let's us add more props with the `props` parameter. It is similar to the box above except we add the background color property `lightblue` in the `Box` declaration in `element`.

```
function Box(props) {
  return (
    <div
      className = "box box--small"
      style = {{paddingLeft: 20}}
      {...props}
     />
  )
}

const element = (
  <div>
      <Box style={{backgroundColor: 'lightblue'}}>small box</Box>
  </div>
)
```

This is what is we see on the browser:

![A box with a light blue background and no border](pictures/style conflict.png)

Due to the recursive nature of the spread operator `...`, the `style` in the element that is passed into the `prop` parameter overwrites the style in the `Box` declaration. So `element`'s `style` is the light blue background overwrites `Box`'s `style` of left padding.

#### Destructuring

Destructuring is a very help tool we can use to remedy this situation. Destructuring is when we unpack values from object properties or values from arrays.

Let's examine a solution with destructuring, step by step:

```
function Box({style, ...rest}) { // 1, then 2
  return (
    <div
      className = "box box--small"
      style = {{paddingLeft: 20, ...style}} // 4
      {...rest} // 3
     />
  )
}

const element = (
  <div>
      <Box style={{backgroundColor: 'lightblue'}}>small box</Box>
  </div>
)
```

1) We make a parameter called `style` in the `Box` declaration.

2) We create another parameter called `...rest` that will take the rest of the props.

3) Any styles are passed in as a continuations of the `style` in the `Box` declaration. This way no code gets overwritten.

4) Any other props that are not styles are added at the end.

We are extracting the property value `lightblue` and making sure it does not conflict with any other code.

We can achieve the same thing with `className`. 

```
function Box({style, className = "", ...rest}) { // 1, then 2
  return (
    <div
      className = {`box ${className}`}
```

We can add our `box--small` or `box--medium` when we make a `Box` in the `element` declaration. Notice how we gave `className` the default value `""`. This is because if we pass nothing, `className` shows up as `undefined` in the DOM since it wouldn't exist.

![The two different results discussed above in the browser dev tools.](pictures/className default value.png)

#### In Practice

Let's see how destructuring styles can be helpful in practice.

We want to make multiple boxes with different sizes and colors. We could apply the `className` and `style` color to each of them. Or, we could destructure the props out and use the destructuring pattern we used above.

```
function Box({style, size, className = "", ...rest}) { // 1, then 2
  const sizeClassName = size ? `box--${size}` : ''
  return (
    <div
      className = {`box ${className} ${sizeClassName}`}
      style = {{paddingLeft: 20, ...style}} // 4
      {...rest} // 3
     />
  )
}
```

In the code above, the code-author does not need to know the precise class names for the different box sizes. We use a string template so the user only types in the size they want, like "small." Destructuring can make our code more user-friendly if we want.

Our boxes look like this:

```
const element = (
  <div>
    <Box 
      size="small"
      style={{backgroundColor: 'lightblue'}}
    >
      small box
    </Box>
    <Box 
      size="medium"
      style={{backgroundColor: 'pink'}}
    >
      medium box
    </Box>
    <Box 
      size="large"
      style={{backgroundColor: 'orange'}}
    >
      large box
    </Box>
  </div>
)
```

![Three different sized boxes with different colors, each with a label.](pictures/different boxes.png)

Destructuring allows us to make flexible React components when styling.

#### TL;DR

1) To style a React component, use the `className` prop to assign CSS classes.

2) You can also use the style prop which has a CSS object as a value

3) Destructuring allows us to make flexible React components when styling.

#### My Thoughts on the Style Property

While the examples above illustrate how the style property works in React, it's considered best practice to keep HTML and CSS separate. I imagine we should try the same and avoid using the style property in React.

### Use Event Handlers with React

This section will discuss how to use event handlers with React.

The event handler examples will be used in this code:

```
const state = {eventCount: 0, username: ''}

function App() {
  return (
    <div>
      <p>
        There have been {state.eventCount} events
      </p>
      <p>
        <button>😄</button>
      </p>
      <p>You typed: {state.username}</p>
      <p>
        <input />
      </p>
    </div>
  )
}

function setState(newState) {
  Object.assign(state, newState)
  renderApp()
}

function renderApp() {
  ReactDOM.render(
    <App />,
    document.getElementById('root'),
  )
}

renderApp()
```

`App` creates a UI. `state` keeps track of the states, one for the button and one for the input. The function `setState` updates our state with whatever we pass into `setState`. `setState` also rerenders with the `renderApp` function everytime we call it.

The UI looks like this:

![The button UI.](pictures/button UI.png)

#### Buttons

We make a function called `increment` and `eventCount` is incremented every time we click the button.

```
function App() {
  return (
    <div>
      <p>
        There have been {state.eventCount} events
      </p>
      <p>
        <button onClick={increment}>😄</button>
      </p>
      <p>You typed: {state.username}</p>
      <p>
        <input />
      </p>
    </div>
  )
}

function increment() {
  setState({
    eventCount: state.eventCount + 1,
  })
}
```

![Clicking the smiley button increments the events.](gifs/onClick event.gif)

We can also do the same thing with `onMouseOver`.

`<button onMouseOver={increment}>😄</button>`.

![Mousing over the smiley button increments the events.](gifs/onMouseOver event.gif)

As well as with `onFocus`.

`<button onFocus={increment}>😄</button>`

![Focusing the smiley button increments the events.](gifs/onFocus event.gif)

#### Input

Input is unique in that it provides an `onChange` event. 

```
<input 
  onChange = {updateUsername}
/>
```

Every time the input changes this function is immediately called. We need the value of the input to update the username state.

We'll make a function called `updateUsername` with `event` as a parameter. 

```
function updateUsername(event) {
  setState({
    username: event.target.value,
  })
}
```
`event.target` gets the input node and `.value` gets the value of the input node. Now `updateUsername` is called whenever we change the input.

![Typing 'Hello world!' in the input is reflected in the UI.](gifs/onChange event.gif)

#### React Event Delegation and Optimization

React has it's own even system where it optimizes things for us with event delegation. There's only one handler for each type on the entire document, which manages calling your event handlers. 

React events are directly on the rendered element. What you pass into the event is a direct reference to the function you want to have called, like how `onFocus` is a direct reference to the function `increment` in `onFocus={increment}`.

This way it is easy to follow the code path of events.

#### TL;DR

Button have various events such as `onClick` or `onFocus`. Input is unique with the event `onChange` that rerenders itself everytime the input changes.

Both button and input events take functions we pass into curly braces. We can write the functions in the curly braces or write the functions outside then pass them in.