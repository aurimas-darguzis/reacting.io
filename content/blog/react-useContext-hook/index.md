---
title: React useContext hook
date: "2019-07-08T23:46:37.121Z"
---

Context is designed to share data that can be considered “global” for a tree of React components, such as the current authenticated user, theme, or preferred language. Context is primarily used when some data needs to be accessible by many components at different nesting levels. Apply it sparingly because it makes component reuse more difficult.

## API building blocks

- `context` object is holding the current context value and can be subscribed to. First step is to create a context with a call to `React.createContext()` - this will return a Context object. If you like to pass a default value - simply pass it in the `createContext({id: 1, category: nutrition })` like so. You are free to pass anything you want. From my personal experience it can be eather a response from http call or internal app state, that replaced redux from quite a few projects.

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

### Getting ready

The best to illustrate is with an example. Let's build an app to book a workout activity. Create a new file called `activityContext.js`. This will be a React component that acts as a wrapper where we set up the Context, and also return that Context's Provider for us to wrap our other components.

```jsx
import React, { useState } from "react"

const ActivityContext = React.createContext()

const ActivityProvider = props => {
  const [state, setState] = useState({
    gymClasses: [
      {
        name: "Vinyasa Flow Yoga",
        booked: false,
      },
      {
        name: "Boxing",
        booked: false,
      },
      {
        name: "TRX Training",
        booked: false,
      },
    ],
  })
  return (
    <ActivityContext.Provider value={[state, setState]}>
      {props.children}
    </ActivityContext.Provider>
  )
}

export { ActivityContext, ActivityProvider }
```

We created a new Context called `ActivityContext` with no arguments - so there is no default value for the context.

We are using `useState` hook to initialize default state and we are passing the state to the Provider.

We also define a new React component called `ActivityProvider` that returns `ActivityContext's` Provider.

Finally we export both the `ActivityContext` and `ActivityProvider` components.

### Updating the Context's state

Let's import our context to `App` component as well as `ActivityList` (we will look into it shortly)

```jsx
import React from "react"

import ActivityList from "./ActivityList"
import { ActivityProvider } from "./ActivityContext"

const App = () => {
  return (
    <ActivityProvider>
      <div className="container">
        <ActivityList />
      </div>
    </ActivityProvider>
  )
}

export default App
```

Let's have a look how can we use our context and update the values from `ActivityList` components.

```jsx
import React from "react"

import { ActivityContext } from "./ActivityContext"

const ActivityList = () => {
  const [state, setState] = useContext(ActivityContext)
  return (
    <>
      {state.gymClasses.map((gymClass, index) => (
        <div className="box">
          <div className="class-title">
            {gymClass.name}
            <button onClick={() => setState(/* update your state */)}>
              {gymClass.booked ? "Booked" : "Book"}
            </button>
          </div>
        </div>
      ))}
    </>
  )
}

export default ActivityList
```

As you can see, the process is stright forward when we want to interact with the context. This example is just a proof of concept and in real world scenario things will get a lot more messy. We will cover those cases in the future blog posts.
