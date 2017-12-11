This is the first part of my notes on egghead.io's [The Beginner's Guide to ReactJS](https://egghead.io/courses/the-beginner-s-guide-to-reactjs).

React is a framework that creates HTML and CSS through methods.

This code snippet below creates HTML and CSS with vanilla JavaScript:

```
<div id="root"></div>
<script type="text/javascript">
  const rootElement = document.getElementById('root')
  const element = document.createElement('div')
  element.textContent = 'Hello World'
  element.className = 'container'
  rootElement.appendChild(element)
</script>
```

First, we create a div with the id "root" with html. Then we write in JavaScript a constant (ES6 syntax) that is assigned that div. Next, we make another div with JavaScript, give it the text "Hello World", and give it a class name of "container." We then nest the new div inside the div with the "root" id.