# React

## Creating Components

* When creating new components, the outer most container element should be a `Component`.
* The `Component` class name should always include the name of the component with `-component` at the end, ie. `hello-component`. Each component should also be given the `component` classname as well. If you are combining strings and variables into classnames, import and use the classnames library which concatenates them for you.
* Use the `pureRenderDecorator` so we can do shallow equality checks on the props. This increases performance by checking new props against old props when deciding whether or not it should rerender.
* Proptypes should be as explicit as possible. If the component is expecting a map, explicitly define the keys and their proptypes in that map.
* You can also set up props from the component's global state instead of the wrapping webpack file by using a connect decorator which will ignore the webpack file and get the initial props directly from the global state. Connector props and props passed in from the parent should be unique.
* Put `render` as the last method.

```javascript
"use strict"

import React from "react"
import Component from "gramercy/components/component"
import pureRenderDecorator from "pure-render-decorator"
import classNames from "clasnames"

function mapStateToProps(state) {
  const image = state.get("image")

  return {
    currentImageIndex: image.getIn(["ui", "currentImageIndex"]),
  }
}

@connect(mapStateToProps)

@pureRenderDecorator
export default class Hello extends React.Component {
  static propTypes = {
    className: React.PropTypes.string,
    name: React.PropTypes.string.isRequired,
    images: ImmutablePropTypes.mapOf(
      ImmutablePropTypes.mapContains({
        imageUrl: React.PropTypes.string,
        imageName: React.PropTypes.string,
      })
    )
  }

  static defaultProps = {
    className: "",
    name: "Jack",
  }

  render() {
    const { className, name } = this.props

    return(
      <Component className={classNames(
          "component"
          "hello-component"
          className,
        )}
      >
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


### Component Handler Naming

When naming events inside a component, use the format of `'handle' + event`. For example, in an email form component the event that submits the form should be called `handleSubmit()` and an event that listens to the handleSubmit action should be called `formSubmitted()` or `submittedForm()`.


### Overriding Styles

If you are using a pre-styled component but need to override those styles for your specific component, wrap the existing component in a new component and use the wrapper component's stylesheet to override those styles.

