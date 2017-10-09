# Real world functional programming in JS

Tips and guidelines for scalable and easily maintainable code bases!

![](https://raw.github.com/fantasyland/fantasy-land/master/logo.png)

## Summary

- [Do](#do)
  - [Return everything](#return-everything)
  - [Use modules](#use-modules)
  - [Composition over inheritance](#composition-over-inheritance)
- [Avoid](#avoid)
  - [Mutability](#mutability)
  - [Loops](#loops)
  - [Try](#try)
  - [Classes](#classes)
- [Advantages](#advantages)
  - [Optimization](#optimization)
  - [Testing](#testing)
  - [Scalability](#scalability)

## Do

### Return everything

Everything should return, also functions that emit side-effects. Try to preserve
homomorphism. Try to keep the return type of a function consistent.

### Use modules

Isolate your logic inside modules that do one thing, and do that well. Modules
should export functions and, when using a type checker, types.

### Composition over inheritance

Inheritance is definitely **not** how you deal with data and behavior in functional programming.
Computations are modeled using behaviors. Some languages call them type classes.

## Avoid

### Mutability

Functional programming heavily relies on immutability. Redefining a value or the property of an
object is therefore forbidden. Don't use neither `var` nor `let`. Everything should be a `const`.
Don't use functions like `.push` or `.splice` because they change the value of the original
parameter and are error-prone.

### Bare code

Code outside a function is a side-effect inside a module. Effects should be explicit. Only
static declarations are allowed.

### Loops

Native statement loops are forbidden. Loops are made to enforce side-effects and there is no
case of a loop where a side-effect wouldn't be expected. You don't need to use recursion most of
the time. There is a lot of functions and compositions that you can use to achieve your logic.

### Try

Error handling shouldn't be handled by exceptions, but by either monads or promises.

### Classes

In general, using classes enforce effects and directly mutability. You can replace them by literal
objects and functions that work on these objects.

## Advantages

### Optimization

Pure functions are easier to optimize. They can cached with their parameters, as long as having
the same input will always generate the same output.

### Testing

TDD is the way to go here. Pure functions are very easier to test and have no dependencies in
general. Having a 100% coverage on a functional code base is a lot easier than in an imperative
one.

### Scalability

Functional code is easier to scale. Having, for example, a stateless server will allow you to
have multiple instances of it in different machines without worrying with shared and dependent
data. Running on clusters and multiple processors is possible and V8 can optimize to run
pure computations on different processes.
