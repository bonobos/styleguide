# PostCSS

## CSS Modules

* When styling a react component with postcss css modules, the root element should always be named `.component` in the local scope.

```javascript

import React from "react"
import Hello from "gramercy/components/hello"
import defaultStyles from "gramercy_css/components/hello.pcss"

export default class Hello extends React.PureComponent {
  static propTypes = {
    className: React.PropTypes.string,
    name: React.PropTypes.string.isRequired,
    styles: React.PropTypes.object,
  }

  static defaultProps = {
    name: "Jack",
  }

  render() {
    const { className, name, styles } = this.props

    return(
      <div className={classNames(
          "component"
          "hello-component"
          styles.component,
          className,
        )}
      >
        Hi {name}!
      </div>
    }

```
