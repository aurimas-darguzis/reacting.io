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

Let's store a user and share the data between two sybiling components.

First let's create `userContext.js` file

```jsx
import { createContext } from "react"
export const UserContext = createContext(null)
```

`App.js`

```jsx
import React from "react"
import { BrowserRouter as Router, Route, Link } from "react-router-dom"
import { Index } from "./pages"
import { About } from "./pages"
import { UserContext } from "./UserContext"

const value = useMemo(() => ({ user, setUser }), [user, setUser])

function AppRouter() {
  return (
    <Router>
      <div>
        <nav>
          <ul>
            <li>
              <Link to="/">Home</Link>
            </li>
            <li>
              <Link to="/about">About</Link>
            </li>
          </ul>
        </nav>

        <UserContext.Provider value={value}>
          <Route path="/" exact component={Index} />
          <Route path="/about" component={About} />
        </UserContext.Provider>
      </div>
    </Router>
  )
}
```

`index.js`

```jsx
import React, { useContext } from "react"
import { UserContext } from "../UserContext"

export function Index() {
  const { user, setUser } = useContext(UserContext)

  return (
    <div>
      <h2>Home</h2>
      <pre>{JSON.stringify(user, null, 2)}</pre>
      {user ? (
        <button
          onClick={() => {
            // call logout
            setUser(null)
          }}
        >
          logout
        </button>
      ) : (
        <button
          onClick={async () => {
            const user = await login()
            setUser(user)
          }}
        >
          login
        </button>
      )}
    </div>
  )
}

const login = async () => {
  return {
    id: 7,
    username: "aurimas",
    email: "aurim@s.com",
  }
}
```

`about.js`

```jsx

import React, { useContext } from "react";
import { UserContext } from "../UserContext";

export function About() {
  const { user } = useContext(UserContext);

  return (
    <div>
      <h2>About</h2>
      <pre>{JSON.stringify(user, null, 2)}</pre>
    </div>
  );

```
