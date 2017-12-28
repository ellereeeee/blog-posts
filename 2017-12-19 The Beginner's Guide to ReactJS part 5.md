![React logo](pictures/React logo.png)

This is the fifth part of my notes on egghead.io's [The Beginner's Guide to ReactJS](https://egghead.io/courses/the-beginner-s-guide-to-reactjs).

### Use Component State with React

This section will show how to build a React app that maintains its own state.  First, we'll create a static UI, then take the dynamic parts and accept them as props. Then we'll refactor that to state and add event handlers to update the state.

#### Make the Static UI

The component `StopWatch` below creates a static UI with some styles. It returns a label and two buttons.

```
function StopWatch() {
  const buttonStyles = {
    border: '1px solid #ccc',
    background: '#fff',
    fontSize: '2em',
    padding: 15,
    margin: 5,
    width: 200,
  }
  return (
    <div style={{textAlign: 'center'}}>
      <label
        style={{fontSize: '5em', display: 'block'}}
      >
        0ms
      </label>
      <button style={buttonStyles}>Start</button>
      <button style={buttonStyles}>Clear</button>
    </div>
  )
}
const element = <StopWatch />
ReactDOM.render(
  element,
  document.getElementById('root'),
)
```

![A static timer UI.](pictures/static timer UI.png)

#### Make It Dynamic

Let's make the parts we want to be dynamic into props.

We add the prop `lapse` to to the label and to the const `element`. We add the prop `running` to the first button and to the const `element`. If `running` is false we get "Start," and if it's true we get "Stop." `lapse` and `element` are initialized in the `element` constant.

```
  return (
    <div style={{textAlign: 'center'}}>
      <label
        style={{fontSize: '5em', display: 'block'}}
      >
        {lapse}ms
      </label>
      <button style={buttonStyles}>{running ? 'Stop' : 'Start'}</button>
      <button style={buttonStyles}>Clear</button>
    </div>
  )
}
const element = <StopWatch running={false} lapse={0} />
```

_Making a static UI first is a good way to start coding a dynamic application. It helps us understand what parts we want to take out and make dynamic._

#### Introduce `state`

Now we'll rewrite the component into a class.

 1) Move everything in the `StopWatch` function into the `StopWatch` class's `render` method.
 
 2) Delete the `StopWatch` function.
 
 Next we could assign `this.props` to our props in curly brackets `const {lapse, running}`, but we want the UI to dynamically maintain state. Instead we
 
 3) **introduce `state` in the class and assign it the props and initial values. `state` is a reserved word in React. This step is key to make our app dynamically maintain state!**
 
 4) Assign `this.state` to our props in curly brackets like this: `const {lapse, running} = this.state`
 
 4) Remove the `lapse` and `running` props in `element` identifier. We initialized these props in step 3.

```
class StopWatch extends React.Component {
  state = {lapse: 0, running: false}
  render() {
    const {lapse, running} = this.state
    const buttonStyles = {
      border: '1px solid #ccc',
      background: '#fff',
      fontSize: '2em',
      padding: 15,
      margin: 5,
      width: 200,
    }
    return (
      <div style={{textAlign: 'center'}}>
        <label
          style={{fontSize: '5em', display: 'block'}}
        >
          {lapse}ms
        </label>
        <button style={buttonStyles}>{running ? 'Stop' : 'Start'}</button>
        <button style={buttonStyles}>Clear</button>
      </div>
    )
  }
}

const element = <StopWatch />
```

Although our UI still hasn't change in appearance, we've layed the groundwork for it to be dynamic. 

#### Make the UI Functional

Let's make the UI functional.

 1) Add `onClick` handlers to the Start and Stop buttons. We give it the value of `this.handleRunClick`.
 
 2) Make `handleRunClick` in the class. Assign an arrow function..
 
 3) The first thing `handleRunClick` returns is the constant `startTime`, which is `Date.now() - this.state.lapse`. This is the time in ms passed since 01.01.1970 minus 0.
 
 4) The second thing `handleRunClick` returns is the method `setInterval`. It updates lapse with `Date.now() - startTime`.
 
 `handleRunClick` updates `startTime` as often as possible with `setInterval`, which reassigns the value of lapse with `Date.now() - startTime`. The first time this runs, it will be `Date.now()` since `lapse` will be initialized as 0. When it runs the second time, it will be `Date.now()` minus the previous `startingTime`, so the time passed is reflected.
 
 5) In `handleRunClick`, update `state` with `this.
 
 5) Add an `onClick` handler to to the Clear button.
 
 6) Make `handleClearClick` in the class. Assign an empty arrow function to it for now.
 
 
9) We make handleRunClick in our code.
10) We make an onClick handler for our clear button and make it in our class.
11) we make the constant startTime Let's examine how this works. First, we make the constant startTime, which is the time in ms passed since 01.01.1970 minus 0. We update it as often as possible with setInterval, which reassigns the value of lapse by Date.now minus startTime. The first time this runs, it will be Date.now - Date.now since lapse is zero. When it runs the second time, it will be Date.now minus the previous startingTime. So the time passed is reflected.  We also making running equal true so the button shows "Stop" once the timer starts.

```
class StopWatch extends React.Component {
  state = {lapse: 0, running: false}
  handleRunClick = () => {
    const startTime = Date.now() - this.state.lapse
    setInterval(() => {
      this.setState({lapse: Date.now() - startTime})
    })
    this.setState({running: true})
  }
  handleClearClick = () => {
    this.setState({lapse: 0, running: false})
  }
  render() {
    const {lapse, running} = this.state
    const buttonStyles = {
      border: '1px solid #ccc',
      background: '#fff',
      fontSize: '2em',
      padding: 15,
      margin: 5,
      width: 200,
    }
    return (
      <div style={{textAlign: 'center'}}>
        <label
          style={{fontSize: '5em', display: 'block'}}
        >
          {lapse}ms
        </label>
        <button onClick={this.handleRunClick} style={buttonStyles}>{running ? 'Stop' : 'Start'}</button>
        <button onClick={this.handleClearClick} style={buttonStyles}>Clear</button>
      </div>
    )
  }
}
```