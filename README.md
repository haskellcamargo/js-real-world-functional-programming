# Real world functional programming in JS

Tips and guidelines for scalable and easily maintanable code bases!

![](https://raw.github.com/fantasyland/fantasy-land/master/logo.png)

## Summary

- [Do](#do)
  - [Return everything](#return-everything)
  - [Use modules](#use-modules)
- [Avoid](#avoid)
  - [Mutability](#mutability)
  - [Loops](#loops)
  - [Try](#try)

## Do

### Return everything

Everything should return, also functions that emit side-effects. Try to preserve
homomorphism. Try to keep the return type of a function consistent.

### Use modules

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
