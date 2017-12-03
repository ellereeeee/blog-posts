![Header image](pictures/2017-11-30 What is Scope header.jpg)

Here are my notes on "What is Scope?," Kyle Simpson's first chapter of [_You Don't Know Javascript: Scope and Closures_](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20%26%20closures/ch1.md).

### The Compilation Process and Scope

Kyle Simpson defines scope as _a well defined set of rules for storing variables in some location, and for finding those variables at a later time._

Despite the fact that JavaScript is often labeled as a "dynamic" or "interpreted" language, it is actually a compiled language. Understanding this is necessary to understand scope in JavaScript.

In a nutshell, compilation consists of three steps before code is executed.

1) *Tokenizing/Lexing* - Code is broken down into meaningful chunks, aka "tokens." For example, in 

   `var a = 2`, 

   `var`, `a`, `=`, and `2` would all be tokens.

2) *Parsing* - tokens are nested into a tree called an AST (Abstract Syntax Tree). It represents the grammatical structure of the program.

3) *Code-Generation* - the process of taking an AST and turning it into executable code. For example, the AST created from `var a = 2` would actually create the `a` variable, etc.

Code is not executed in those three steps. Code in JavaScript is usually executed immediately after.

### Understanding Scope

There are three main "players" involved when understanding scope.

1) *Engine* - responsible for start-to-finish compilation and execution of our JavaScript program

2) *Compiler* - handles parsing and code-generation before code execution

3) *Scope* - collects and maintains a look-up list of all declared identifiers (variables) and enforces a strict set of rules for how these are accessible through currently executing code

Two distinct things happen in a variable assignment in JavaScript, such as in `var a = 2`. First, the compiler declares a variable if not previously found in the current scope (`a`), and then the engine Engine looks up the variable (`a`) in the scope and assigns to it (`2`), if found.

There are two types of identifier "look ups" the Engine performs when executing code. Simpson calls these "LHS" and "RHS" looks up, meaning Left Hand Side or Right Hand Side of the assignment operator "`=`". The important thing to know is that there are two kinds of reference look ups: looking up the target of an assignment (LHS), and looking up the source of an assignment (RHS).

Here is my solution to the quiz in this chapter. You have to identify all LHS (3) and RHS (4) looks ups.

```
function foo(a) { // line 1
	var b = a;    // line 2
	return a + b; // line 3
}                 // line 4
                  // line 5
var c = foo( 2 ); // line 6
```

From the start of execution:

1) RHS look up for `foo` function on line 6. Function found in line 1.

2) LHS look up for `a` variable when assigning `2` on line 6 to the `a` parameter in the function.

3) RHS look up for `a` on line 2. Found as a parameter in line 1.

4) LHS look up for `b` when assigning `a`'s value of 2

5) RHS look up for `b` on line 3. Found in line 2.

6) RHS look up for `a` on line 3. Found in line 1.

7) LHS look up for `c` varible on line 6 when assigning `foo(2)`.

The flow of execution starts at assigning the variable `2` to `foo(a)`, executing that function, then assigning the result (`4`) to `var c`.

### Nested Scope

When a LHS or RHS look-up is made, the engine looks for the reference in the current scope (which is either a block of code or a function). If it's not present, it then searches the next outer scope and continues until it either finds the reference or the global scope is reached.

For example: 

```
function foo(a) {
	console.log( a + b );
}

var b = 2;

foo( 2 ); // 4
```

The RHS reference for `b` is resolved not in the `foo` function but in the outer (global, in this case) scope.

### Errors

When not in strict mode, LHS and RHS look-ups have different results when a reference is not found. Failed RHS look-ups throw a `ReferenceError`. The global scope implicitly creates a variable during a failed LHS look-up.

In strict mode, failed LHS and RHS look-ups both throw a `ReferenceError`.

A `ReferenceError` indicates a scope resolution failure while `TypeError` indicates a reference was found, but an impossible action was attempted.