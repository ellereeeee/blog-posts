![]()

These are my notes on "Behavior Delegation," Kyle Simpson's [sixth chapter](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch6.md) of _You Don't Know Javascript: this and Object Prototypes_.

## Toward Delegation-Oriented Design

The `[[Prototype]]` mechanism represents a different design pattern from classes.

### Class Theory

Let's say we have similar tasks that we need to model in our code.

With classes, you would define a general parent (base) class like `Task` and define shared behavior for all the similar tasks.