## JS Interview prep

Contents.

1. JS Basics
2. Functional JS
3. Objects
4. Async
5. Advanced JS
6. Storage

### Basics

#### Variables

- Loosely-typed, JS will figure it out
- Three ways to declare variables:
  - `var`
    - functionally or globally scoped
    - can be redeclared
    - can be declared without initialization
    - can be updated
  - `let`
    - block scoped
    - cannot be redeclared within its scope
    - can be declared without initialization
    - can be updated
  - `const`
    - block scoped
    - cannot be re-declared within its scope
    - must be initialized at declaration
    - can never be updated

```
var a = 3
var a = 4

console.log(a) // 4 as var variables can be redeclared + updated

let b = 3
let b = 4

console.log(b) // Syntax Error as let variables cannot be redeclared

// If we just do, it will work because it can be updated
b = 4

const c = 3
const c = 4

console.log(c) // Syntax Error as const variables cannot be redeclared or updated

const d

// will throw an error, const must be initialized
```

#### == vs ===

- `==` : checks only value
- `===` : checks value + type
- _eli: will use type coercion for `==`_

```
let a = 5 // number
let b = '5' // string

console.log(a == b) // true

console.log(a === b) // false
```

#### Arrays

- initalize: `const arr = [4, 4, 'hello']`
- array methods:
  - `map`
  - `filter`
  - `forEach`
    - can mutate the array during callback
    - won't visit indices add after `forEach` invocation
    - run for side effects
    - no method chaining (returns undefined)

### Functional programming

- two ways to define a function
  - `function a() { }`
  - `const a = () => { }`

#### Scope

- Global (declaration outside of any function)
- Function (declare inside a function)
- Block (declare inside a block)

Contrived example:

```
var a = 5 // we can access this a anywhere

function adder(){
    let b = 7
    console.log(a + b)
 }

console.log(adder())

console.log(b) // Error as b is not accessible outside the function

{
const c = 10
console.log(c) // 10
}

console.log(c) // Error as c is not accessible outside the block
```

#### Closures

Closed-over variable `prefix`:

```
const greet = () =>  {
    const prefix = 'Mr'
    return (name) => {
        console.log(`${prefix} ${name}, welcome!`)
    }
}

console.log(greet()('Jack'))
```

Closure is a function bundled together with its lexical environment.

Good for:

1. currying
2. data hiding/encapsulation

Bad:

1. can cause overconsumption of memory - closed-over variable will not be garbage collected

#### Hoisting

- `var` is hoisted an initialized to `undefined`
- `let` and `const` are hoisted but not initialized
- `function` is hoisted and stored as-is

```
function consoleNum() {
  console.log(num)
  var num = 10
}

consoleNum() // undefined

// Why no error?

// This is how runtime sees this
{
  var num
  console.log(num)
  num = 9
}

// If instead of var -> let, it will give an error as let values are not initialized
```

### Objects

- Implicit binding - `this` is bound due to the invocation
  - `this` refers to the calling object, left of the `.`, as in `myObj.funcCall()` will refer to `myObj`
- Explicit binding - force a particular `this` usage

```
const student_1 =  {
    name: 'Randall',
    displayName_1: function displayName() {
        console.log(this.name)
    }
}
const student_2 =  {
    name: 'Raj',
    displayName_2: function displayName() {
        console.log(this.name)
    }
}

student_1.displayName_1.call(student_2) // Raj
```

- Explicit binding options:
  - `call` runs instantly, accepts a list of items (comma-separated args)
  - `apply` runs instantly, accepts an array
  - `bind`, assigns scope for use later, accepts list of items

```
// similar example to above

const myData = {
  name: 'Rajat',
  city: 'Delhi',
  displayStay: function () {
    console.log(this.name, 'stays in', this.city)
  },
}
myData.displayStay()

// create an object yourData and try to use displayStay
const yourData = {
 name: 'name',
 city: 'city'
}


// answer
myData.displayStay.call(yourData)
```

- arrow functions' `this` value depends on lexical scope (i.e. no implicit binding)

#### Arrow functions vs. explicit function calls

- Arrow functions don't have the `arguments` object
- Arrow functions don't create a `this` binding

```
const obj = {
  name: 'deeecode',
  age: 200,
  print: function() {
    function print2() {
      console.log(this)
    }

    print2()
  }
}

obj.print()
// Window, because no object invokes print2
```

Now, with an arrow function instead:

```
const obj = {
  name: 'deeecode',
  age: 200,
  print: () => {
    console.log(this)
  }
}

obj.print()
// Window - arrow function just refers to the outer context before the creation of the object
```

But,

```
const obj = {
  name: 'deeecode',
  age: 200,
  print: function() {
    const print2 = () => {
      console.log(this)
    }

    print2()
  }
}

obj.print()
// {
//   name: 'deeecode',
//   age: 200,
//   print: [Function: print]
// }

// in the above case, the print function creates a `this` context using the object, and because print2 is an arrow function, it just uses that outer context
```

- No arrow functions allowed as constructors
- Arrow functions cannot be accessed before initialization. `function` keyword functions will be hoisted to the top of the global scope before the code is executed, however, standard `const` or `let` variables can't be access before initialization. `var` can be accessed but will be initialized to `undefined` as the default prior to being set programmatically

#### Prototypes

- Everything in JS gets some basic properties and methods in the `__proto__` object

```
let arr = ['Rajat', 'Raj']
console.log(arr.__proto__.forEach)
console.log(arr.__proto__) // same as Array.prototype
console.log(arr.__proto__.__proto__) // same as Object.prototype
console.log(arr.__proto__.__proto__.__proto__) // null
```

### Asynchronous

- JS is single-threaded

```
  for (var i = 1; i <= 5; i++) {
    setTimeout(function () {
      console.log(i)
    }, i * 1000)
  }

// output
6
6
6
6
6
```

- Because `i` is globally scoped, every log is referencing the same `i`. Using let would print 1 2 3 4 5.
- Always clear intervals

#### Promises

- promise represents the eventual completion of an async JS operation and its resulting value
- three states:
  - pending: initial state, neither fulfilled nor rejected
  - fulfilled: operation was completed successfully
  - rejected: operation failed

### Advanced concepts

#### Polyfills

- polyfills to provide modern functionality for older browsers
- map polyfill:

```
// this - array
// this[i] - current value
Array.prototype.myMap = function (cb) {
  var arr = []
  for (var i = 0; i < this.length; i++) {
    arr.push(cb(this[i], i, this))
  }
  return arr
}

const arr = [1, 2, 3]
console.log(arr.myMap((a) => a * 2)) // [2, 4, 6]
```

#### Async & defer

- All about how scripts are laoded when parsing HTML
- Scripts inside `<head>` tag:
  - No async or defer used: when a script is encountered, HTML parsing is paused while the script is fetched and subsequently executed
  - Async used (`<script async src="..">`): encountered script content is fetched asynchronously while parsing continues. Parsing is then paused to execute the script.
  - Defer used (`<script defer src=".."`): encountered script content is fetched asynchronously while parsing continues. Script is executed after parsing is fully complete.
- Scripts inside `<body>` tag:
  - Scripts are both fetched and executed after parsing is complete
- When scripts are deferred, they are executed in the order in which they are defined. This is not guaranteed when `async` is used, so `async` should be used when the script is not dependent on other scripts

#### Debounce & throttle

- Debounce will call a function only after a certain amount of time has passed without the function being called again
- Example implementation which will wait for 300ms of function inactivity:

```
const getData = (e) => {
  console.log(e.target.value)
}
const inputField = document.getElementById('text')

const debounce = function (fn, delay) {
  let timer
  return function () {
    let context = this
    clearTimeout(timer)
    timer = setTimeout(() => {
      fn.apply(context, arguments)
    }, delay)
  }
}

inputField.addEventListener('keyup', debounce(getData, 300))
```

- throttle ensures a function will be only be called on time during a given time interval
- example implementation for 300ms throttle:

```
const expensive = () => {
  console.log('expensive')
}

const throttle = (fn, limit) => {
  let context = this
  let flag = true
  return function () {
    if (flag) {
      fn.apply(context, arguments)
      flag = false
    }
    setTimeout(() => {
      flag = true
    }, limit)
  }
}
const func = throttle(expensive, 2000)
window.addEventListener('resize', func)
```

- debouncing is when the difference between the successive triggering events is 300ms. Throttling is when the difference between function calls is 300ms

#### Storage

- `localStorage` persists after closing your session
- `sessionStorage` doesn't. data is lost when you close the browser on the tab

```
// save
localStorage.setItem('key', 'value')
// get saved data
let data = localStorage.getItem('key')
// remove saved data
localStorage.removeItem('key')
// Same for sessionStorage

```
