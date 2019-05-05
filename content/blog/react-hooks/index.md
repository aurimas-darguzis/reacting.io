---
title: React hooks
date: "2019-05-04T23:46:37.121Z"
---

Hooks were added in React with 16.8 version. Essentialy, hooks let you use state and other React features without writing a class. What's another good thing is, that this new feature is completely optional - you don't have to rewrite your application, and it's backwards compatible.

## What is a hook?

Hooks are JavaScript functions. They let you "hook into" the React state and use lifecycle features from function components. Hooks doesn't work in React classes at all, only in functional components. React introduced built-in hooks that we can all use - basic hooks, such as `useState`, `useEffect`, `useContext` and additional ones: `useReducer`, `useCallback`, `useMemo`, `useRef`, `useImperativeHandle`, `useLayoutEffect`, `useDebugValue`.

## Problem

If you look at a typical React application, you will likely find a "wrapper hell" of components surrounded by layers of providers, consumers, higher-order components, render props, and other abstractions. React needs a better primitive for sharing stateful logic. With Hooks, we can extract stateful logic from a component so it can be tested independently and reused. Hooks allow to reuse stateful logic without changing component hierarchy.
