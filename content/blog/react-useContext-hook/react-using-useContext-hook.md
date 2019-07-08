---
title: React useContext hook
date: "2019-07-08T23:46:37.121Z"
---

Context is designed to share data that can be considered “global” for a tree of React components, such as the current authenticated user, theme, or preferred language. Context is primarily used when some data needs to be accessible by many components at different nesting levels. Apply it sparingly because it makes component reuse more difficult.

## API building blocks

- `context` object is holding the current context value and can be subscribed to. First step is to create a context with a call to `React.createContext()` - this will return a Context object. If you like to pass a default value - simply pass it in the `createContext({id: 1, category: nutrition })` like so. You are free to pass anything you want. It will probably end up being an object with few properties on it, or an array that you later on will iterate to create items on the screen. This can be a response from http call, or just internal state object.

```js
const MyContext = React.createContext(defaultValue)
```

- `provider` is a React component that provides the value. It grabs it from the context object and the value will be passed to consuming components that are descendants of this Provider. All consumers that are descendants of a Provider will re-render whenever the Provider’s value prop changes. Changes are determined by comparing the new and old values using the same algorithm as `Object.is`.

```js
<MyContext.Provider value={/* some value */}>
```

- `consumer`is a React component that is able to consume the value provided by the provider and is able to show the value. Requires a function as a child. The function receives the current context value and returns a React node. The value argument passed to the function will be equal to the value prop of the closest Provider for this context above in the tree.

```js
<MyContext.Consumer>
  {value => /* render something based on the context value */}
</MyContext.Consumer>
```

## Example

The best to illustrate is to set up an example and show a real-ish use case for the context. Let's say we have an app, where our users can book a room in a hotel. And as developers we decide not to use something like redux, but actually we want to implement contex API for the tas. Let's start by creating our own `context.js` file. First step is easy and strightforward - initialise a context.

```jsx
import React from "react"

const RoomContext = React.createContext()
```

To use context throughout the app we need to wrap our component tree into `RoomContext.Provider` and pass a value to it. But, we can also have a provider in the same `context.js` file. This gives us way more flexibility - we can get the data in the same file, adjust it as we need it to and pass it on.

```jsx
export default function RoomProvider() {
  return <RoomContext.Provider value={"hello"}>{children}</RoomContext.Provider>
}

export default RoomConsumer = RoomContext.Consumer
```

To be able to access context throughout our application, we can place wrap provider at the top level of our application:

```jsx
import React from "react"
import ReactDOM from "react-dom"
import App from "./App"
import { BrowserRouter as Router } from "react-router-dom"
import { RoomProvider } from "./context"

ReactDOM.render(
  <RoomProvider>
    <Router>
      <App />
    </Router>
  </RoomProvider>,
  document.getElementById("root")
)
```

https://www.youtube.com/watch?v=ScDWrogElmo 1h:46min
