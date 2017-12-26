![React logo](pictures/React logo.png)

This is the fifth part of my notes on egghead.io's [The Beginner's Guide to ReactJS](https://egghead.io/courses/the-beginner-s-guide-to-reactjs).

### Use Component State with React

This section will show how to build a React app that maintains its own state.  First, we'll create a static UI, then take the dynamic parts and accept them as props. Then we'll refactor that to state and add event handlers to update the state.

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


Let's make the parts we want to be dynamic into props.

We add the prop `lapse` to to the label and to the const `element`. We add the prop `running` to the first button and to the const `element`. If `running` is false we get "Start," and if it's true we get "Stop."

