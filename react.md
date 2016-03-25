# React

## Creating Components

* When creating new components, the outer most container element should be a `Component`.
* The `Component` class name should always include the name of the component with `-component` at the end, ie. `hello-component`.
* Use the `pureRenderDecorator` so we can do shallow equality checks on the props. This increases performance by checking new props against old props when deciding whether or not it should rerender.
* Put `render` as the last method.

```javascript
"use strict"

import React from "react"
import Component from "gramercy/components/component"
import pureRenderDecorator from "pure-render-decorator"

@pureRenderDecorator
export default class Hello extends React.Component {
  static propTypes = {
    className: React.PropTypes.string,
    name: React.PropTypes.string.isRequired,
  }

  static defaultProps = {
    className: "",
    name: "Jack",
  }

  render() {
    const { className, name } = this.props

    return(
      <Component className={`hello-component ${className}`}>
        Hi {name}!
      </Component>
    }

```

### Wrapping a Component

Sometimes you just want to wrap a component to change the props in a reusable way. You might think to create a new component that renders the component you want, but you should probably be using [Stateless Functions](https://facebook.github.io/react/docs/reusable-components.html#stateless-functions):

```javascript
"use strict"

import React from "react"
import Hello from "gramercy/components/hello"

export default (props) => {
  const { className, firstName, lastName, ...other } = props
  const fullName = `${firstName} ${lastName}`

  return (
    <Hello
      className="hello-first-last-name-component"
      name={fullName}
      {...other}
    />
  )
}
```

You can use this just like a regular component, but because it has no state and all you're doing it wrapping a component, the syntax is a lot lighter and you don't need to worry about validating propTypes twice.
