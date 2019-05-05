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

When components logic is all over the place - it is difficult to test them, hard to break into smaller pieces. Hence engineers prefer to combine React with a state management library. This comes with a lot boiler plate code, introducing too much of abstraction, to achieve a task usualy you need to open few files. This makes resuing of component more difficult.

Hooks let you split one component into smaller functions based on what pieces are related - such as setting up a subscription or fetching data, rather than forcing a split based on lifecycle methods. To make state more predictable you can opt into managing the component's local state with a reducer.

Developers can understand props, state, and top-down data flow perfectly well but we still struggle with classes. The distinction between function and class componetn in React and when to use each one leads to disagreements event between experienced React developers.

Another problem with React classes is that classes don't minify very well, they make hot reloading flaky and unreliable. We need an API taht makes it more likely for code to stay on the optimizable path. React Hooks lets us use more of React's features without classes.

Conceptually, React components have always been closer to functions. Hooks embrace functions, but without sacrificing the practical spirit of React. Hooks provides access to imperative escape hatches and don't require to learn complex functional or reactive programming techniques.
