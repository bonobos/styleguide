# Javascript (ES6)

We are running all js code (client & server) through [babel](https://babeljs.io/) so favor writing with ES6 in mind.

## Variables

##### :x: Don't use `var`
`var` is mutable, and globally scoped. There shouldn't be any use case for using it.

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

With ES6, there are now multiple ways to declare functions in javascript. There are _slight_ differences in behavior with different function declarations, so we'll cover that here and when they should be used.

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

**For almost every case, you probably want to use an `arrow` function**

##### :white_check_mark: Use for callbacks & anonymous functions
```js
// good
setTimeout(() => {
 console.log("foo")
}, 100)
```

##### :white_check_mark: Use when exporting a function
```js
// good
export const myFunction = (arg1, arg2) => {
  console.log(arg1, arg2)
}
```

##### :white_check_mark: Use for storing function in variable
```js
// good
const makeSquare = (n) => { return n * n }
```

##### :white_check_mark: use parentheses for arguments
Parentheses are optional for 0 or 1 arguments. Always use parentheses for consistency.
```js
// good
const sayHi = () => { console.log("Hi") }

// good
list.forEach((item) => { /* do something */ })
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

### `function` keyword

The `function` keyword should **_only_** be used for creating local (not exported) functions. They should also only be used at the top-level of the file (not inside another function).

##### :white_check_mark: Use for standalone functions
```js
// good
function makeSquare(n) {
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

##### :x: Avoid using for exported functions
```js
// bad
export function myFunction() {
 ...
}
```


## Dependencies

### Exporting

##### :white_check_mark: Place `default export` at bottom of file for any React Component.
When specifying a `default` export, make the export line the last line in the file.

```js
const MyComponent =  () => (
  { /* contents */ }
)

export default MyComponent
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

// Next, Redux actions (if applicable)
import {
  productDetailAddToCartClicked,
  productDetailOptionSelected,
} from "highilne/redux/actions/product_detail_actions"

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

## Asynchronous Code

##### ✅ Use `async / await` for handling asynchronous code

Since we're using babel to transpile both client & server code, we can take advantage of the latest javascript features. This means we can write asynchronous code using the `async / await` syntax instead of `Promise`s.

For instance, instead of writing:

```js
// bad

function callApi() {
  return new Promise((resolve, reject) => {
     const response = // make ajax call
     if (response.success)
       resolve(response)
     else
       reject(response)
  }
}

callApi
  .then((response) => { console.log("Success", response) })
  .catch((error) => { console.log("Error", error) })
```

We can now write our code in a more procedural manner using `async / await`

```js
// good

async function callApi() {
  const response = await // make ajax call
  return response
}

try {
  const response = await callApi()
  console.log("Success", response)
} catch (error) {
  console.log("Error", error)  
}
```

##### ✅ Use `Promise.all` for resolving multiple `async` calls

There's not yet a standard way for `awaiting` multiple async calls, so the preferred approach (for now) is to use `Promise.all` (since `async` is technically just syntactic sugar around promises)

```js
// good
const responses = await Promise.all([apiCall1, apiCall2, apiCall3])
responses.forEach((response) => {
  console.log(response)
})
```

# React

## Importing React

Importing react: 
```js
import React from "react"
```

isn't required due to our build process, but we should add it to the top of each file that requires `React` regardless. This will help if files are ever used outside of our build, and also less confusing for people who might not know why it still works.

## Declaring a Component

##### :white_check_mark: Use `React.PureComponent` if you need state, `ref`, or lifecycle functions

If you need an instance of a react component (tapping into state or a lifecycle function), use a class that extends `React.PureComponent`. 

_exceptions:_ Components in `layouts` or `admin` can be exempt from this rule.

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

##### :white_check_mark: Order fields & functions by react lifecycle on a Component

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
// components/shared/my_component.js
export default myComponent
```

##### :white_check_mark: Components should be either scoped to a _feature specific_ or declared at the top level.

If a component is a basic building block (not specific to any feature), it should live in the top level of the `components` directory.

```js
/* good */
highline/components/button.js
highline/components/input.js
highline/components/input_with_label.js
highline/components/product_detail/options.js
highline/components/product_detail/product_summary.js
```

##### :white_check_mark: Use `on` prefix for callbacks in components. Use `handle` for callback handlers in containers
When exposing a prop that gets called based on some action (`onClick`, `onChange` etc..), use the `on` prefix when defining the prop in a component. Use `handle` when defining the prop in a container.

```js
// good
<MyComponent
  onClick={ this.componentClicked }
  onChange={ this.componentChanged }
/>

const mapDispatchToProps = {
  handleInputChange() {
    dispatch(somethingChanged())
  }
}

// bad
<MyComponent
  clicked={ this.componentClicked }
  handleChange={ this.componentChanged }
/>
```

# Redux

## Naming Conventions

### ActionTypes
All actions that get dispatched should be declared in the **_past tense_**. That is, they should represent the result of some action _that has already happened_. If you find yourself naming an action for something that _should_ happen, think about what is the actual thing that is triggering the need for that action.

```js
// good
PRODUCT_FETCH_STARTED
PRODUCT_FETCH_FAILED
PRODUCT_FETCH_SUCCEEDED
CART_OPEN_CLICKED

// bad
FETCH_NEW_PRODUCTS
OPEN_CART
OPEN_MODAL
```

### API Call Lifecycle
We have a specific naming convention for actions that correspond to different states of the HTTP request cycle. For each asynchronous action that makes an API call, there are be 4 actions that correspond the each phase of the request

```js
_STARTED     // before request is sent
_SUCCEEEDED  // on request success
_FAILED      // on request error
_COMPLETED   // after request done (regardless of success/error) 
```


### Actions
There are two types actions that can be dispatched to redux. Those are `action creators` (return an `Object`), and `action thunks` or, "`async`" actions (return a `function`). 

##### :white_check_mark: Action creators should be prefixed with the actionType
Action Creators are just functions that return a plain javascript `Object`, that has a `type` field. Convention is to use the action type prefix in the name.

```js
// good
export const productDetailAddToCartClicked = (sku) => ({
    type: ActionTypes.PRODUCT_DETAIL_ADD_TO_CART_CLICKED,
    sku,
})

// bad
export const addToCartClicked = (sku) => ({
    type: ActionTypes.PRODUCT_DETAIL_ADD_TO_CART_CLICKED,
    sku,
})
```

##### :white_check_mark: Thunks (`async` actions) should be postfixed with `Async`
Async actions are functions that return a `function` to be evaulated at a later time. It doesn't necessarily mean there needs to be some type of `i/o` operation, but just that there is some additional logic based on the application state to be performed. Async actions can also _dispatch other actions_. The action type prefix is not needed, since there isn't a 1-to-1 correlation of action & action type 

**(but maybe we should keep the prefix for consistency?)**

```js
export const addToCartAsync = (sku) => (
  (getState, dispatch) => {
    dispatch(productDetailAddToCartStarted())
    ...
    return dispatch(productDetailAddToCartSuccess())
  }
)
```
