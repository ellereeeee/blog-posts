![React logo](pictures/React logo.png)

This is the seventh part of my notes on egghead.io's [The Beginner's Guide to ReactJS](https://egghead.io/courses/the-beginner-s-guide-to-reactjs).

### Make Basic Forms with React

#### Preventing Default Behavior

Let's start with a basic input and submit button in a form element:

```
class NameForm extends React.Component {
  render() {
    return (
    <form>
      <label>
        Name:
        <input type="text" />
        <button type="submit">Submit</button>
      </label>
    </form>
    )
  }
}

ReactDOM.render(
  <NameForm />,
  document.getElementById('root'),
)
```

![The page refreshes when the submit button is pressed.](gifs/submit default refresh.gif)

By default, submit refreshes the page. To fix this, add the `onSubmit` event handler to `form`. Then assign `this.handleSubmit` to `onSubmit`. Next, add the property `handleSubmit` in the class body (this is a public class field we discussed previously) and assign it an arrow function that takes `event` as an argument and returns `event.preventDefault()`.

```
class NameForm extends React.Component {
  handleSubmit = event => {
    event.preventDefault()
  }
  render() {
    return (
    <form onSubmit={this.handleSubmit}>
      <label>
        Name:
        <input type="text" />
        <button type="submit">Submit</button>
      </label>
    </form>
    )
  }
}
```

![The page does not refresh when the submit button is pressed](gifs/prevent default.gif)

Now the page doesn't refresh anymore when the submit button is pressed.

#### Getting the Input Value

There are three ways to get the input value.

1) The **target event property**

We can use `event.target` to return the element that triggered the event. By assigning `event.target` to a property in an object and logging it to the console, we can see all the properties of `form`.

Our code looks like this:

```
  handleSubmit = event => {
    event.preventDefault()
    console.log({target: event.target})
  }
```

And we can find in the console the value of the input.

![Finding the value of the form input in the console with the target event property.](gifs/target event property.gif)

Assigning `event.target` to a property in an object literal and logging it in React will log the data to the console in a JSON style format, similar to `JSON.stringify` or `console.dir(...)`.

In the console we click on `{target: form}`, then `target:form`, then `0:input`, and finally on the `(...)` value under `value` to see the string `"Richard"` or whatever is submitted in the form.

2) With this knowledge, we could get the value with **`event.target[0].value`**.

3) The third way is by adding the `name` prop to the `input`.

Like this: `<input type="text" name="username"/>`

We can then find the `value` of the input in the console.

![Finding the value of the form input in the console with the name value prop.](gifs/name prop value.gif)

Click on `{target: form}`, then `target: form`, then `elements`, `username`, and finally on the `(...)` value under `value`.

We could get the input similar to number 2 with `event.target.elements.username.value`.

Logging this to the console:

```
    console.log({target: event.target})
    console.log(event.target[0].value)
    console.log(event.target.elements.username.value)
```

And submitting `Richard` would show this:

!["Richard" is submitted and {target: form}, Richard and Richard appear in the console.](pictures/basic forms console.png)