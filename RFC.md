# Proposed style guidelines for Highlnie (javascript)

This doc will eventually become the updated styleguide for how we write javascript in highline. Using the RFC as a way to comment & track the progress of making sure the code is consistent across the codebase for the styles we agree to.

Each style rule reference will be accompained by a issue for making sure:
  - [ ] Style rule is finalized (agreed upon)
  - [ ] Up to date (all occurances in codebase have been updated to follow rule)
  - [ ] ESlint compatible (we can enforce style through an ESlint rule, and the eslint rule has been added)
  
 This way, we can track each rule's progress through a pull request (rather than having giant PRs for refactoring multiple rules at a time).
 
 There will also be a Slack channel for discussion around rules, for more ad-hoc questions (that aren't best addressed through comments on the RFC)

This RFC will probably be updated frequenetly, and feel free to request that any rules be added.

Keep in mind this RFC is for _proposals_ for the styleguide, not necessarily how we're currently doing it.

With that said, ON TO THE JAVSCRIPT :tada:

# Javascript (ES6)

We are running all js code (client & server) through [babel](https://babeljs.io/) so favor writing with ES6 in mind.

## Variables

##### :x: Don't use `var`
`var` is mutable, and globall scoped. There shouldn't be any use case for using it.

##### :white_check_mark: Use `const` as default
We should always favor immutability. It makes the code safer, and easier to reason about. If you find yourself re-assigning a variable, it's a good indication the code could be written in a more functional manner. For instance, instead of
```js
// bad
var foo
if (n == 1)
  foo = "bar"
else
  foo = "baz"
```
It could be re-written
```js
// good
const foo = (n == 1) ? "bar" : "baz"
```

##### :large_orange_diamond: Use `let` sparingly
There are some instances where it makes sense to re-assign a variable. In those cases, use `let`, as it's scoped to the nearest function (opposed to `var`, which is scoped globally)

## Function Declaration 

With ES6, there are now multiple ways to declare functions in javascript. There are _slight_ differences in behavior with different function delcarations, so we'll cover that here and when they should be used.

### `function` keyword

In javascript, the `function` keyword can be used to declare _and_ name a function. 

The `function` keyword should be used when declaring a function outside of any lexical scope. 

##### :white_check_mark: Use for standalone functions (with no reference to `this`)
```js
// good
function makeSqaure(n) {
  return n * n
}

makeSquare(2) // 4
```

Avoid using the `function` keyword without a name (such as a callback) or storing into a variable. 

##### :x: Avoid using for callbacks
```js
// bad
setTimeout(function() {
  console.log("hi")
}, 1000)
```

##### :x: Avoid using for inline or storing into a variable
```js
// bad
const myFunction = function() {
  return "hi"
}
```

### Arrow Functions `() => {}`

ES6 introduced the concept of an `arrow` function. That is, the ability to create a function without the use of the `function` keyword. An arrow function behaves the same as a non-arrow function, _with the exception_ that the function is always bound the context where it's _defined_, and not where it's called. 

For instance, declaring an `arrow` function:
```js
const sayHi = () => {
  console.log("Hi " + this.name)
}
```

Is the equivalent to:
```js
function sayHi() {
  console.log("Hi " + this.name)
}.bind(this)
```

##### :white_check_mark: Use for callbacks & anonymous functions
```js
// good
setTimeout(() => {
 console.log("foo")
}, 100)
```

##### :white_check_mark: Use for storing function in varaible
```js
// good
const makeSquare = (n) => { return n * n }
```

##### :white_check_mark: use parentheses for arguments
Parentheses are optional for 0 or 1 arguments. Always use parentheses for consistency.
```js
// good
const sayHi = () => { console.log("Hi") }
```

##### :white_check_mark: favor using implicit returns
With arrow functions, you can specificy an _implicit_ return if the function has only one execution statement, and the function is not defined with braces. For instance, the following are equivalent
```js
const makeSquare = (n) => { return n * n }

// preferred
const makeSquare = (n) => n * n
```
This is common use for performing a single operation over a collection
```js
// Sqaure all items in list
list.map(
  (item) => item * item
)
```

## Importing Dependencies

### Exporting

##### :white_check_mark: Place `default export` at bottom of file
When specifying a `default` export, make the export line the last line in the file.

```js
function someFunction () {
  ...this does a bunch of stuff
}

export default someFunction
```

##### :white_check_mark: use `function` declaration for exporting functions
There's no need to implicity `bind` exported functions to where they're delcared. This also prevents the context from being changed (if needed)

```js
// good
export function makeSquare(n) {
  return n * n
}

// bad
export const makeSquare(n) => {
  return n * n
}
```


### Ordering
Imports can be specified in any order, but it might be good to have rules around how they should be declared to offer some sanity. 

If anyone has any suggestions, that would be great, but I've sort of been doing the following:

```js
// First, 3rd-party dependencies
import React from "react"
import { fromJS } from "immutable"
...

// Next, local functions & components
import * as CartApi from "highline/api/cart_api"
import { formatUrl } from "highline/utils/url"
import CartComponent from "highline/components/cart"

// Next, Redux components (if applicable)
import store from "highilne/redux/store"

// Lastly, styles from css
import styles from "highline/styles/style.css"
```

### Naming
The imported library/function name should match the name & casing of the default export being imported.

```js
// good
import classnames from "classnames"

// bad
import cn from "classnames"

// bad
import classNames from "classnames"
```

# React

## Importing React

Importing react: 
```js
import React from "react"
```

isn't required due to our build process, but we should add it to the top of each file that requires `React` regardless. This will help if files are ever used outside of our build, and also less confusing for people who might not know why it still works.

## Delcaring a Component

##### :white_check_mark: Use `React.PureComponent` if you need state, `ref`, or lifecycle functions

If you need an instance of a react component (tapping into state or a lifecycle function), use a class that extends `React.PureComponent`. 

*note:* `PureComponent` will implement `componentShouldUpdate` with a shallow comparison of `props`, to determine if the `render()` function should be called. For the most part, this is fine since we are using `immutablejs`, but this should be kept in mind if the component is not re-rendering when it should.

```jsx
class MyComponent extends React.PureComponent {
  state = {
    name: "bonobos",
  }

  render() {
    const { name } = this.state

    return (
      <span>Hi { name }</span>
    )
  }
}
```

##### :white_check_mark: Use a _stateless functional_ component if you don't need state or lifecycle functions
**_this one is up for discussion_**

If you don't need state, or any of React's built in functions, you can use a function to define the component instead of a class. Some benefits to this approach:
  * Less overhead (react doesn't need to maintain an instance of a class in memory)
  * Simplicity (easier to read, reason about since there's not instance or state based logic)
  
To create a stateless functional component, define a function that takes props, and returns a React (or DOM) element

```jsx
const myComponent = ({
 name,
}) => (
 <span>{ name }</span>
)

export default myComponent
```

##### :white_check_mark: Use `arrow` syntax to bind a function to a react component
A common pattern is to have an instance function on a component that is bound to that instance. By declaring the function using the `arrow () => {}` syntax, the function will be bound to the created instance. This avoids the need to bind the function explicitly.

```js
class MyComponent extends React.PureComponent {
  constructor(props) {
    super(props)
    
    // bad
    this.myFunction = this.myFunction.bind(this)
  }

  // good
  myFunction = () => {
    this.setState({ ... })
  }

  render() {
   return (
    <OtherComponent
      onClick={ this.myFunction } // good
      onChange={ myFunction.bind(this) } // bad
    />
   )
  }
}
```

#### :white_check_mark: Order fields & functions by react lifecycle on a Component

Order functions in the same order that the react-lifeccyle happens. That is:

```
optional static methods
constructor
getChildContext
componentWillMount
componentDidMount
componentWillReceiveProps
shouldComponentUpdate
componentWillUpdate
componentDidUpdate
componentWillUnmount
... custom methods here ...
render
```

```js
class MyComponent extends React.Component {
  static propTypes { ... }
  constructor() { ... }
  componentWillMount() { ... }
  componentDidMount() { ... }
  onButtonClick = () => { ... }
  render() { ... }
}
```

##### :white_check_mark: Sort props alphabetically

When declaring props (either in `propTypes` or on a Component, always sort alphabetically)

```jsx
<MyComponent
  name="bonobos"
  onClick={ onClick }
  zIndex={ 20 }
/>
```

##### :white_check_mark: Destructure props & state

Arguments to functions are not declared as const, so destructring the props in the `render` method is useful for forcing immutability, but also help with checking against typos and using undeclared variables.

```jsx
// good
render() {
  const {
    name,
    onClick,
    zIndex,
  } = this.props

  return (
    <MyComponent
      name={ name }
      onClick={ onClick }
      zIndex={ zIndex }
    />
  )
}
```

##### :x: Don't reference `this.props` or `this.state` in `render`
Referencing `this.props` or `this.state` directly in render can lead to calling undefined (or misspelled) props. By pulling into a variable, it's clear which variables are in scope (and used) within the render function.

```jsx
// bad
render() {
  return (
    <MyComponent
      name={ this.props.name }
      onClick={ this.props.onClick }
      zIndex={ this.props.zIndex }
    />
  )
}
```

## Naming Conventions

##### :white_check_mark: Each component filename should match the name of the default export

```jsx
// components/my_component.js
export default myComponent
```

##### :white_check_mark: Components should be either _feature specific_ or _base_ components

To organize components, we should label a component as something that's a base-building block (button, form, list, icon, etc...), or a _feature specific_ component (homepage, product, category, etc...). Every component should fall into some directory under `/components`

```js
/* good */
// highline/components/base/button.js
// highline/components/base/input.js
// highline/components/base/inputWithLabel.js
// highline/components/product_detail/options.js
// highline/components/product_detail/product_summary.js

/* bad */
// highline/componnets/myComponent.js
```

