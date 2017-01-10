# React

## Creating Components

* The component `className` should always include the `component` class name and the name of the component with `-component` at the end, ie. `hello-component`. Each component should also be given the `component` classname as well. If you are combining strings and variables into classnames, import and use the classnames library which concatenates it for you.
* Proptypes should be as explicit as possible. If the component is expecting a map, explicitly define the keys and their proptypes in that map.
* Put `render` as the last method.

```javascript

import React from "react"
import classNames from "clasnames"

function mapStateToProps(state) {
  const image = state.get("image")

  return {
    currentImageIndex: image.getIn(["ui", "currentImageIndex"]),
  }
}

@connect(mapStateToProps)

export default class Hello extends React.PureComponent {
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
    name: "Jack",
  }

  render() {
    const { className, name } = this.props

    return(
      <div className={classNames(
          "component"
          "hello-component"
          className,
        )}
      >
        Hi {name}!
      </div>
    }

```

### Stateless functional component vs PureComponent vs Component

Use [stateless functional component](https://facebook.github.io/react/blog/2015/10/07/react-v0.14.html#stateless-functional-components) as your default.

Unless, you need...

1. To use state.
2. Component life cycle methods.
3. To use `ref`.

then extend [PureComponent](https://facebook.github.io/react/docs/react-api.html#react.purecomponent).

`PureComponent` does shallow equality checks against prop changes. This increases performance by checking new props against old props when deciding whether or not it should re-render. This must used in conjunction with `immutable` props since you can't shallow equality check primitive javascript arrays and objects.

Unless, you also need...

1. To override or add a custom `shouldComponentUpdate` method.

then extend [Component](https://facebook.github.io/react/docs/react-api.html#react.component).


### Presentational vs Container Components

Also called Smart vs Dumb Components, is outlined in detail [here](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0#.kbocrg9qf) by Dan Abramov. We've started to do this. Try to design your dumb components first in storybook, so you can work with them in isolation. This might help you design the props to be more reusable. Then when you want to inject state, create a smart container that wraps the dumb component and provides it with callbacks and props it needs to render.

### Wrapping a Component

Sometimes you just want to wrap a component to change the props in a reusable way. You might think to create a new component that renders the component you want, but you should probably be using [Stateless Functions](https://facebook.github.io/react/docs/reusable-components.html#stateless-functions):

```javascript
import React from "react"
import Modal from "gramercy/components/modal"
import classNames from "classnames"
import defaultStyles from "gramercy_css/components/full_screen_modal.pcss"

const FullScreenModal = (props) => {
  const { className, ...other } = props

  return (
    <Modal
      className={classNames(
        "full-screen-modal-component",
        className,
      )}
      {...other}
    />
  )
}

FullScreenModal.propTypes = {
  className: React.PropTypes.string,
  styles: React.PropTypes.object,
}

FullScreenModal.defaultProps = {
  styles: defaultStyles,
}

export default FullScreenModal
```

You can use this just like a regular component, but because it has no state and all you're doing it wrapping a component, the syntax is a lot lighter and you don't need to worry about validating propTypes twice.


### Component Handler Naming

When naming events inside a component, use the format of `'handle' + event`. For example, in an email form component the event that submits the form should be called `handleSubmit()` and an event that listens to the handleSubmit action should be called `formSubmitted()` or `submittedForm()`.


### Overriding Styles

If you are using a pre-styled component but need to override those styles for your specific component, wrap the existing component in a new component and use the wrapper component's stylesheet to override those styles.
