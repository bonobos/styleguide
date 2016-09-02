# Javascript

## Importing dependencies

Where possible, try to import specific functions, variables/constants, objects,
rather than importing an entire library. Importing everything means that
the entire file/library gets loaded on the client side. You can potentially
reduce page weight by only importing the thing you need.

```javascript
// bad

import Immutable from "immutable"

const a = Immutable.List()
const b = Immutable.Map()
const c = Immutable.Set()

// good

import { List, Map, Set } from "immutable"

const a = List()
const b = Map()
const c = Set()
```

## Arrow functions

Prefer ES6 arrow function syntax over `function` syntax for closures:

```javascript
// bad

const x = function(y) {
  return x + 1
}

// good

const x = (y) => {
  return x + 1
}
```

Closure arguments should always be in parentheses:


```javascript
// bad

x => x +1

// good

(x) => x +1
```
