---
title: React demystifying hooks
date: "2019-06-13T23:46:37.121Z"
---

Does it ever occured to you when you call `useState` for two different props in your component and it returns exactly what you need?

```jsx
const [minutes, setMinutes] = useState(5) // minutes = 5
const [error, setError] = useState(null) // error = null
```

Let's make our own version of `useState` React Hooks to demystify how they work.

Here we go - let's declare a `useState` function and pass `defaultValue` to it as an argument. We expect to get back from `useState` a value and a function - so I will introduce `const state = [defaultValue, setValue]` and return it. Now `setValue` is an inner function that will be responsible for assigning new value to the state. With every new value - we need to tell React to re-render the component. Here's our function sceleton so far:

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

How to set a new value? If we would try inside `setValue` something like `defaultValue = newValue` - that just wouldn't work. Anything we do inside here will be garbage collected once we are done with the function so it's not the right path to go. We need to keep track of all states outside `useState` function. It's as easy as `const states = []`. Inside this constant we will push all states that our component will use.

In order to push all states to our variable we need to somehow to know how to access it in the right place. For that - let's create `const calls = -1`. Its job is to keep track of how many calls we have done to `useState`. By referencing this constant inside `useState` function we can have something like `const callId = ++calls`. Using `++calls` means that it is going to increment value and return it. Opposed to `calls++` would simply increment value. Once we have `callId`, now we are able to insert correct state into correct place insde `setValue`. Also, we push our new state into global `states` array.

```js
...
const callId = ++calls
const setValue = newValue => {
  // assign new value
  states[callId][0] = newValue
  // re-render
}

const state = [defaultValue, setValue]
states[callId] = state
return state
...
```

After we assign a new value to our state and return it, we need to re-render the component. I will introduce a new function that does just that `renderWithMyHooksImplementation()` and call it inside `setValue` function.

```js
const setValue = newValue => {
  states[callId][0] = newValue
  rerender()
}
```

Inside `rerender()` implementation we need to reset `calls` variable and don't forget to call the function as well, so our app gets initial render.

```js
function rerender() {
  calls = -1
  ReactDOM.render(<App />, document.getElementById("root"))
}
rerender()
```

So far so good? We are nearly there to be honest. Let's see how our state changes with small example:

```js
const [minutes, setMinutes] = useState(5)
const [error, setError] = useState(null)
```

- The first time component renders we are calling `useState` 2 times
- The first time we call `useState` and pass `5` as default value
- Our `calls = 0`
- Inside `useState()` our `callId = 0`
- Inside `setValue` we have `states[0][0]` pointer to update `minutes`
- The second time we call `useState` and pass `null` as default value
- `calls = 1`
- `callId = 1`
- `states[1][0]` points to `error` state
- Our global `states` now is looking like this: `[[5, fn], [null, fn]]`

What happens if we call `setMinutes` now? The answer is - we will end up with `states` having 3 values inside: `[[5, fn], [null, fn], [6, fn]]`. The problem is, that we keep incrementing our `callId` inside the function. To resolve this issue we need to check if we already have `states[callId]` existing.

```js
if (states[callId]) {
  return states[callId]
}
```

If so - then we just simply return it and here we go - we just end up with our own `useState` hook implementation

```js
const states = []
let calss = -1

function useState(defaultValue) {
  const callId = ++calls

  if (states[callId]) {
    return states[callId]
  }

  const setValue = newValue => {
    states[callId][0] = newValue
    rerender()
  }
  const state = [defaultValue, setValue]
  states[callId] = state
  return state
}
```

Obviously React team implemented hooks in a lot more complicated way, because components mount and unmount, you actually need to keep track of the call count per component. If the component comes in and use the state and then goes out - then we need to clean up things. So this illustration here is a mere mental model on how to look at how hooks works. This is why we have those rules of hooks, where you can't run hooks inside an `if` statement, because the order of `useState` calls matter.
