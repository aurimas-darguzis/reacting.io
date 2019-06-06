---
title: Intro to React hooks
date: "2019-05-04T23:46:37.121Z"
---

Hooks were added in React with 16.8 version. Essentialy, hooks let you use state and other React features without writing a class. What's another good thing is, that this new feature is completely optional - you don't have to rewrite your application, and it's backwards compatible.

## What is a hook?

Hooks are JavaScript functions. They let you "hook into" the React state and use lifecycle features from function components. Hooks doesn't work in React classes at all, only in functional components. React introduced built-in hooks that we can all use - basic hooks, such as `useState`, `useEffect`, `useContext` and additional ones: `useReducer`, `useCallback`, `useMemo`, `useRef`, `useImperativeHandle`, `useLayoutEffect`, `useDebugValue`.

## Problem

If you look at a typical React application, you will likely find a "wrapper hell" of components surrounded by layers of providers, consumers, higher-order components, render props, and other abstractions. React needs a better primitive for sharing stateful logic. With Hooks, we can extract stateful logic from a component so it can be tested independently and reused. Hooks allow to reuse stateful logic without changing component hierarchy.

Components always starts simple but grows into stateful logic with side effects in no time. Each lifecycle method contains a mix of unrelated logic. We do data fetching in `componentDidMount` and `componentDidUpdate`. Sometimes the same `componentDidMount` method contains unrelated logic that sets up an event listener, which cleanup performed in `componentWillUnmount`. Related code that changes together gets split apart, and completely unrelated code ends up combined in a single method. It's an easy way to introduce bugs and inconsistencies.

When components logic is all over the place - it is difficult to test them, hard to break into smaller pieces. Hence engineers prefer to combine React with a state management library. This comes with a lot boiler plate code, introducing too much of abstraction, to achieve a task usualy you need to open few files. This makes reusing of component more difficult.

Hooks let you split one component into smaller functions based on what pieces are related - such as setting up a subscription or fetching data, rather than forcing a split based on lifecycle methods. To make state more predictable you can opt into managing the component's local state with a reducer.

Developers can understand props, state, and top-down data flow perfectly well but we still struggle with classes. The distinction between function and class componetn in React and when to use each one leads to disagreements event between experienced React developers.

Another problem with React classes is that classes don't minify very well, they make hot reloading flaky and unreliable. We need an API taht makes it more likely for code to stay on the optimizable path. React Hooks lets us use more of React's features without classes.

Conceptually, React components have always been closer to functions. Hooks embrace functions, but without sacrificing the practical spirit of React. Hooks provides access to imperative escape hatches and don't require to learn complex functional or reactive programming techniques.

## Rules

### 1. Only call Hooks at the top level

Never call Hooks inside loops, conditions, or nested functions. Instead we need to use Hooks at the top level of React function. This ensures that Hooks are called in the same order each time a component renders. That's what allows React to correctly preserve the state of Hooks between multiple `useState` and `useEffect` calls.

### 2. Only call Hooks from React functions

Don't call Hooks from regular JavaScript functions.

- Call Hooks from React function components
- Call Hooks from custom Hooks

By following this rule, you ensure that all stateful logic in a component is clearly visible from its source code.

To enforce these two rules there is ESLint plugin `eslint-plugin-react-hooks. You can add this plugin to your project:

```sh
npm install eslint-plugin-react-hooks --save-dev
```

```json
{
  "plugins": [
    // ...
    "react-hooks"
  ],
  "rules": {
    // ...
    "react-hooks/rules-of-hooks": "error", // Checks rules of Hooks
    "react-hooks/exhaustive-deps": "warn" // Checks effect dependencies.
  }
}
```

## Example: Using the State Hook

Let's start learnign about state Hook by comparing code to an equivalent class example

In a class, we initialize state in a constructor and using `this.state`:

```jsx
class Eample extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      price: 0,
    }
  }
}
```

In a function component, we have no `this`, so we can't assign or read `this.state`. Instead, we call the `useState` Hook directly inside our component:

```jsx
import React, { useState } from "react"

function Example() {
  const [price, setPrice] = useState(0)
}
```

`useState` declares a state variable. In this case it's called `price` but we could call it anything we want. `useState` is a new way to use the exact same capabilities that `this.state` provides in a class. The only argument to the `useState()` Hook is the initial state. Unlike with classes the state doesn't have to be an object. We can pass a number or a string, if that's all we need. `useState()` returns a pair of values: the current state and a function that updates it. This is why we have `const [price, setPrice] = useState(0)`. This is similar to `this.state.price` and `this.setState` in a class.

When we want to display the current price in a class, we read `this.state.price`:

```jsx
<p>Your total price: {this.state.price}</p>
```

In a function, we can use `price` directly:

```jsx
<p>Your total price: {price}</p>
```

In a class, we need to call `this.setState()` to update the `price` state:

```jsx
<button onClick={() => this.setState({ price: this.state.price + 15 })}>
  Add product
</button>
```

In a function, we already have `setPrice` and `price` as variables, so we don't need `this`:

```jsx
<button onClick={() => setPrice(price + 15)}>Add product</button>
```

### Recap

Let's now recap what we learned so far with this example:

```jsx
import React, { useState } from "react"

function Example() {
  const [price, setPrice] = useState(0)

  return (
    <div>
      <button onClick={() => setPrice(price + 15)}>Add product</button>
      <p>Your total price: {price}</p>
    </div>
  )
}
```

We import `useState` Hook from React. It lets us keep local state in a function component. Inside the `Example` component we declase a new state variable and a method to update it. We also pass initial value for the state property when we call `useState(0)`. When a user clicks button, we call `setPrice` with a new value. React then re-render the `Example` component, passing the new `price` value to it.

### What do square brackets mean?

```jsx
const [price, setPrice] = useState(0)
```

The names on the left aren't a part of React API. you can name them something else. This JavaScript syntax is called "array destructuring". It means that we're making two new variables `price` and `setPrice`, where `price` is set to the first value returned by `useState`, and `setPrice` is the second. It is equivalent to this code:

```jsx
const priceStateVariable = useState(0) // Returns a pair
const price = priceStateVariable[0] // First item in a pair
const setPrice = priceStateVariable[1] // Second item in a pair
```

When we declare a state variable with `useState`, it returns a pair - an array with two items. The first item is the current value, and the second is a function that lets us update it.

### Using multiple state variables

We are not bound to using just one state variable declaration in our component. We can use as many as we need:

```jsx
function Products() {
  const [price, setPrice] = useState(0)
  const [vegetable, setVegetable] = useState(5)
  const [fruit, setFruit] = useState(3)
}
```

In the above component we have `price`, `banana` and `apple` as local variables, and we can update them individually:

```jsx
function handleAppleClick() {
  setFruit("apple")
}
```

You don't have to use many state variables. State variables can hold objects and arrays just fine, so you can still group related data together. However, unlike `this.setState` in a class, updating a state variable always replcaes it instead of merging it.

### Should you use Hooks, classes or a mix of both?

According to official React documentation - it's highly encouraged to start using Hooks in new components that you write. There is no need to rewrite class components into using Hooks though. Both concepts can get along just fine, as long as you don't mix them up in a single file. You can definitely mix classes and function components with Hooks in a single tree. React team stated that their goal is for Hooks to cover all use cases for classes as soon as possible. It is still an early time for Hooks, and some third-party libraries might not be compatible with them at the moment.

## Example: How to test components that use Hooks?

A component using Hooks is just a regular React component. It shouldn't be different from how you normallu test your components. Let's take counter component as an example:

```jsx
function TestExample() {
  const [count, setCount] useState(0)
  useEffect(() => {
    document.title = `You clicked ${count} times`
  })
  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me!
      </button>
  )
}
```

Let's test this code snippet using React DOM. To make sure that the behavior matches what happens in the browser, let's wrap the code rendering and updating it into `ReactTestUtils.act()` calls:

```jsx
import React from "react"
import ReactDOM from "react-dom"
import { act } from "react-dom/test-utils"
import Counter from "./Counter"

let container

beforeEach(() => {
  container = document.createElement("div")
  container.body.appendChild(container)
})

afterEach(() => {
  document.body.removeChild(container)
  container = null
})

it("can render and update a counter", () => {
  act(() => {
    ReactDOM.render(<Counter />, container)
  })
  const button = container.querySelector("button")
  const label = container.querySelector("p")
  expect(label.textContent).toBe("You clicked 0 times")
  expect(document.title).toBe("You clicked 0 times")

  act(() => {
    button.dispatchEvent(new MouseEvent("click", { bubbles: true }))
  })
  expect(label.textContent).toBe("You clicked 0 times")
  expect(document.title).toBe("You clicked 0 times")
})
```

The calls to `act()` will also flush the effects inside of them.

If you need to test a custom Hook, you can do so by creating a component in your test, and using your Hook from it. Then you can test the component you wrote.

To reduce the boilerplate, it's recommended using `react-testing-library` which is designed to encourage writing tests that use your components as the end users do.
