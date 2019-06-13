---
title: React demystifying hooks
date: "2019-06-13T23:46:37.121Z"
---

Does it ever occured to you when you call `useState` for two different props in your component and it returns exactly what you need?

```jsx
const [minutes, setMinutes] = useState(5) // minutes = 5
const [error, setError] = useState(null) // error = null
```

Let's make our own version of React Hooks to demystify how they work.

We start with passing the default value and we need to return an array that consist of value with the function to set it. In our example, when we want to `setMinutes`, we are going to to call `setValue` function with the new value. So now we need to keep track of original value, assign this new value and also make sure we re-render the component.

```js
function useState(defaultValue) {
  const setValue = newValue => {
    // assign new value
    // re-render
  }
  const state = [defaultValue, setValue]
  return state
}
```

Now the challenge is - how do we keep the value of state as well as setting the new value. If we are going to keep all that inside `useState` function, then as soon as we done with it - the garbage collection will simply get rid of it. So we need to track our state outside this function we are building. Let's create `states` variable and assign to an empty array. Here is the place where we are going to push all our states. We also need to get some kind of identifier to know exact place to update it. So we introduce `calls` variable. This variable is going to keep track of how many calls we have done to `useState`. Inside the function we can now have `callId` and this helps us to know exactly what to update inside the `states` array.

We also need to re-render every time we set a new value. So let's introduce new function that does just that and call it `renderWithMyHooksImplementation`.

Last thing we need to make sure is after the first render, when we stored our each state in `states` array - let's just return it. So our fist render we bring in the default value, we create `setValue` function and then we store it in the `states`. On the second render we come around and we are checking if we are already tracking this state so we don't need to create a brand new one, instead we can just return it.

```js
const states = []
let calss = -1

function useState(defaultValue) {
  const callId = ++calls // ++calls will increment the value and return it

  if (states[callId]) {
    return states[callId]
  }

  const setValue = newValue => {
    // assign new value
    states[callId][0] = newValue
    // re-render
    renderWithMyHooksImplementation()
  }
  const state = [defaultValue, setValue]
  states[callId] = state
  return state
}
```

Don't forget to reset the `calls` after each render, because otherwise - our state array will grow with each render indefinitely and we will not be able to know exact place where we need to update it.

```js
function renderWithMyHooksImplementation() {
  calls = -1
  ReactDOM.render(<App />, document.getElementById("root"))
}
renderWithMyHooksImplementation()
```

So let's re-cap what do we have at the moment:

- The first render comes around, we increment `callId` to 0
- we create state with `[defaultValue, setValue]`
- assign it to the global `states` array in the right place, by using `callId` like this `states[callId] = state`
- the second render comes around, then we check `if (states[callId])` - do we have this state already in the array? If yes, then we are going to use it. This way we are not creating new state every time, but rather updating it.

To illustrate with our small example here, if we have:

```js
const [minutes, setMinutes] = useState(5)
const [error, setError] = useState(null)
```

Our state after the first render will look like this:

```js
const states = [[5, fn], [null, fn]]
```

Our `states[0]` will always update `minutes` value and the `states[1]` will always refer to the `error` variable.

Obviously React team implemented hooks in a lot more complicated way, because components mount and unmount, you actually need to keep track of the call count per component. If the component comes in and use the state and then goes out - then we need to clean up things. So this illustration here is a mere mental model on how to look at how hooks works. This is why we have those rules of hooks, where you can't run hooks inside an `if` statement, because the order matters.
