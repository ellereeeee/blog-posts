![React logo](pictures/React logo.png)

#### Write Hello World with raw React APIs

This is the first part of my notes on egghead.io's [The Beginner's Guide to ReactJS](https://egghead.io/courses/the-beginner-s-guide-to-reactjs).

React is a framework that creates HTML and CSS through methods.

This code snippet below creates HTML and CSS with vanilla JavaScript:

```
<div id="root"></div> // make div
<script type="text/javascript">
  const rootElement = document.getElementById('root')
  const element = document.createElement('div') // make another div
  element.textContent = 'Hello World' // html to div
  element.className = 'container' // add class to div
  rootElement.appendChild(element) // nest new div in div with id "root"
</script>
```

First, we create a div with the id "root" with html. Then we write in JavaScript a constant (ES6 syntax) that is assigned that div. Next, we make another div with JavaScript, give it the text "Hello World", and give it a class name of "container." We then nest the new div inside the div with the "root" id.

The following code achieves the same thing in React:

```
<div id="root"></div>
<script crossorigin src="https://unpkg.com/react@16/umd/react.production.min.js"></script>
<script crossorigin src="https://unpkg.com/react-dom@16/umd/react-dom.production.min.js"></script>
<script type="text/javascript">
  const rootElement = document.getElementById('root')
//  const element = document.createElement('div')
//  element.textContent = 'Hello World'
//  element.className = 'container'
//  rootElement.appendChild(element)
  const element = React.createElement( // create another div with `createElement` method
    'div', 
    {className: 'container'},
    'Hello World'
  ) // the first argument is the element, the second is properties, the last is children
  ReactDOM.render(element, rootElement) // similar to append, element is rendered to rootElement
</script>
```

The commented out code from the first snippet is replaced in functionality by React. First we make a constant with **the `createElement()` method that takes three arguments: the html element, properties, and children of the element.** If we want, we can add more children. Last we have a **`render()` method that renders** `element` to `rootElement`.

What happens when we run `console.log(element);`?

![Console snapshot of React element object](pictures/React element object.png)

The important thing to note is the `props` object. It contains the properties `children` and `className` which here corresponds to the inner HTML and CSS class name.

If we have more than one argument for the last parameter, we would have an array for `children`. For example, this it what we would say if we had

```
  const element = React.createElement( // create another div with `createElement` method
    'div', 
    {className: 'container'},
    'Hello World',
    'Goodbye World
```

![Console snapshot of React element object with array in props](pictures/React element object 2.png)

The output would be the same if we wrote the array.

```
  const element = React.createElement(
    'div', 
    {className: 'container',
     children: ['Hello World', 'Goodbye World']
    })
```

The third parameter for children is for convenience.