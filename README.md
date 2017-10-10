# Real world functional programming in JS

Tips and guidelines for scalable and easily maintainable code bases!

![](https://raw.github.com/fantasyland/fantasy-land/master/logo.png)

## Summary

- [Recommended libraries](#recommended-libraries)
  - [Ramda](#ramda)
  - [Ramda Fantasy](#ramda-fantasy)
- [Do](#do)
  - [Return everything](#return-everything)
  - [Tacit programming](#tacit-programming)
  - [Use modules](#use-modules)
  - [Composition over inheritance](#composition-over-inheritance)
  - [Memoize functions](#memoize-functions)
- [Avoid](#avoid)
  - [Mutability](#mutability)
  - [Loops](#loops)
  - [Switch](#switch)
  - [Try](#try)
  - [Undefined and null](#undefined-and-null)
  - [Classes](#classes)
  - [Callbacks](#callbacks)
  - [Prototype extension](#prototype-extension)
- [Advantages](#advantages)
  - [Optimization](#optimization)
  - [Testing](#testing)
  - [Scalability](#scalability)
- [Learning resources](#learning-resources)
  - [Motivational articles](#motivational-articles)
  - [For beginners](#for-beginners)
  - [Babel plugins](#babel-plugins)

Functional programming in JavaScript in 2017 is finally a **DO**. Most of the
new tools for this environment are based on functional concepts and it seems
like functional programming is becoming more and more popular due to its
advantages. On [Rung](https://developers.rung.com.br/), functional programming
is the main mindset for all projects and subprojects and it scales really well.
We have been using functional programming deeply in the last 2 years, from
JS up to Haskell. Bugs got less frequent and easier to track and fix, and,
different from what you may think, our team got it **really fast**! Keeping
the code standard and working with several people without worrying with
ones breaking each other code is now a relief! The tips below are not empiric,
they are being used daily by us to deliver high quality code.

## Recommended libraries

### Ramda

[Ramda](https://github.com/ramda/ramda) is like Lodash, but made right. It has a lot
of functions to work with data and to work with composition. Unlike Lodash, in
Ramda, functions come before data. This allows point-free programming (like Haskell).

### Ramda Fantasy

[Ramda Fantasy](https://github.com/ramda/ramda-fantasy) is a set of common monadic
structures to work with values, errors, side-effects and state.

## Folktale

[Folktale](http://folktale.origamitower.com/) is an alternative to Ramda Fantasy. It
is better maintained and exports tools for working with concurrency, computations that
may fail and modelling data using algebraic data types.

## Do

### Return everything

Everything should return, also functions that emit side-effects. Try to preserve
homomorphism. Try to keep the return type of a function consistent. Don't mix
computations that transform data with things like writing to screen. Modular code
is easier to maintain. Use parameters for replaceable data instead of hard-coding.

#### Don't

```js
let result = 1;
for (let i = 2; i <= 5; i++) {
    result *= i;
}

console.log('Fact of 5: ', result);
```

#### Do

```js
const fact(n) = n === 0
    ? 1
    : n * fact(n - 1);

console.log('Fact of 5: ', fact(5));
```


### Tacit programming

Tacit programming is also known as point-free programming. It means, basically, using
simple functions to compose more complexes functions (and omitting arguments). One of
the functional programming pillars is composition.

### Don't

```js
console.log(
    sum(map(x => x + 1, range(0, 100)))
)
```

### Do

```js
const transform = pipe(map(inc), sum))

console.log(transform(range(0, 100)))
```

### Use modules

Isolate your logic inside modules that do one thing, and do that well. Modules
should export functions and, when using a type checker, types.

### Composition over inheritance

Inheritance is definitely **not** how you deal with data and behavior in functional programming.
Computations are modeled using behaviors. Some languages call them type classes.

### Memoize functions

Memoize pure functions that are used several times with the same input parameters. Ramda provides
a function called `memoize` for that!

#### Do

```js
const fact = memoize(n => 0 === n
    ? 1
    : n * fact(n - 1))

fact(5); // Calculates fact for 5, 4, 3 ...
fact(5); // Instantaneous
fact(3); // Instantaneous
```

## Avoid

### Mutability

> Mutability is evil! It can set your house on fire, kill your cat and buy costumes on e-bay
> using your credit card! Be careful!

Functional programming heavily relies on immutability. Redefining a value or the property of an
object is therefore forbidden. Don't use neither `var` nor `let`. Everything should be a `const`.
Don't use functions like `.push` or `.splice` because they change the value of the original
parameter and are error-prone.

#### Don't

```js
var approved = []

for (var i = 0; i < approved.length; i++) {
    if (users[i].score >= 7) {
        approved.push(approved)
    }
}
```

#### Do

```js
const approved = filter(user => user.score >= 7, users)
```

### Bare code

Code outside a function is a side-effect inside a module. Effects should be explicit. Only
static declarations are allowed.

#### Don't

- `user.js`
```js
const usersList = User.find().asArray()

usersList.forEach(user => {
    console.log(user.name)
})
```

#### Do

- `user.js`
```js
const listUsers = () => User.find().asArray()

export const printNames = () => listUsers().forEach(user => {
    console.log(user.name)
})
```

- `index.js`
```js
import { printNames } from './user'

printNames()
```

### Loops

Native statement loops are forbidden. Loops are made to enforce side-effects and there is no
case of a loop where a side-effect wouldn't be expected. You don't need to use recursion most of
the time. There is a lot of functions and compositions that you can use to achieve your logic.
Ramda provides some functions like `map`, `filter` and `reduce` that make loops completely
unnecessary.

#### Don't

```js
const even = [];
for (let i = 0; i <= 300; i++) {
    if (i % 2 === 0) {
        even.push(i)
    }
}

console.log(even) // [0, 2, 4, 6, 8 ...]
```

#### Do

```js
import { filter, range } from 'ramda'

const even = filter(n => n % 2 === 0)

console.log(even(range(0, 300))) // [0, 2, 4, 6, 8 ...]
```

### Switch

In functional programming, imperative structures do not exist. `switch` is intended to have
effects and known to have a complex flux. You can use the function `cond` from Ramda instead.
`cond` receives a list of pairs of functions where the first one is the predicate and the
second is the transformation.

#### Don't

```js
const person = { name: 'Wesley' }
let result;

switch (person.name) {
    case 'Dayana':
        result = person.name + ' is cool!'
        break
    case 'Wesley':
        result = person.name + ' likes farting'
        break
    default:
        result = 'Who is ' + person.name + '?'
}

console.log(result) // Wesley likes farting
```

### Do

```js
import { T, cond, propEq } from 'ramda'

const getDescription = cond([
    [propEq('name', 'Dayana'), ({ name }) => `${name} is cool!`],
    [propEq('name', 'Wesley'), ({ name }) => `${name} likes farting`],
    [T, ({ name }) => `Who is ${name}?`]
])

console.log(getDescription({ name: 'Wesley' })) // Wesley likes farting
```

### Try

Error handling shouldn't be handled by exceptions, but by either monads or promises.

#### Don't

```js
try {
    undefined.property
} catch (err) {
    console.log(err)
}
```

#### Do

```js
import { tryCatch } from 'ramda'
import { Either } from 'ramda-fantasy'

const computation = tryCatch(
    () => undefined.property,
    Either.Right,
    Either.Left
)

console.log(computation()) // Left<TypeError>
```

### Undefined and null

Here lies the root of _undefined is not a function_. Missing values lead to over-engineering,
lots of verifications and conditions and errors that break your application and cannot
be caught in compile-time. You should replace them by monads, like the `Maybe` monad.

#### Don't

```js
const saveDif = (a, b) => {
    if (b === 0) {
        return undefined
    }

    return a / b
}

console.log(safeDiv(20, 0) + 10) // Ops
```

#### Do

```js
import { Maybe } from 'ramda-fantasy'

const safeDiv = (a, b) => 0 === b
    ? Maybe.Nothing
    : Maybe.Just(a / b)

safeDiv(20, 0).chain(result => {
    console.log(result + 10) // Never falls here
})
```

### Classes

In general, using classes enforce effects and directly mutability. You can replace them by literal
objects and functions that work on these objects.

#### Don't

```js
class Person {
    setName(name) { this.name = name }
    getName() { return this.name }
}

let person = new Person()
person.setName('Cassandra')
```

#### Do

```js
import { lensProp, prop, set } from 'ramda'

const setName = set(lensProp('name'))
const getName = prop('name')

const person = setName('Cassandra', {})
```

### Callbacks

Callbacks can guide you easily to _Hadouken_ code. Promises or _futures_ are the way to go here.
If you are using a library that uses callbacks, you can _promisify_ the function to transform the
callback to a promise.

#### Don't

```js
$.ajax('http://api.trello.com/me', me => {
    $.ajax(`http://api.trello.com/tasks/${me.id}`, tasks => {
        var finished = []
        for (var i = 0; i < tasks.length; i++) {
            if (tasks[i].done) {
                finished.push(tasks[i])
            }
        }
    })
})
```

#### Do

```js
fetch('http://api.trello.com/me')
    .then(({ id }) => `http://api.trello.com/tasks/${id}`)
    .filter(prop('done'))
    .tap(console.log)
```

### Prototype extension

Doing something like `String.prototype.x =` is like a virus! It spreads all over your code and
infects every piece, possibly causing unexpected behavior. Prefer isolating these behaviors in
functions.

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

# Learning resources

## Motivational articles

- [Functional Programming should be your #1 priority](https://medium.com/@jugoncalves/functional-programming-should-be-your-1-priority-for-2015-47dd4641d6b9)

## For beginners

- [Functors, Applicatives, And Monads In Pictures](http://adit.io/posts/2013-04-17-functors,_applicatives,_and_monads_in_pictures.html)

## Babel plugins

- [Function composition](https://github.com/haskellcamargo/babel-plugin-function-composition)
- [Implicit function](https://github.com/haskellcamargo/babel-plugin-implicit-function)
- [Pipe operator](https://github.com/Swizz/babel-plugin-pipe-operator-curry)
