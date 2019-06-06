---
title: React Suspense
date: "2019-05-31"
---

Using Suspense to enhance the way your application loads module, assets and data.

## Problem

Loading stuff is hard. When building an application a lot of time and energy is focused on coordinating on handling incoming data, making a decision what to show or hide to the user. You are striving to make your application fast, so you optimise for performance - try to lazy load your bundle, split data into component parts so the user can get to the most important stuff on page as fast as possible. In doing so, you may introduce a few places where data comes in from different sources, you show a few spinners while the user waits, and once data comes in - one by one those spinners are replaced with the data. This is not the best user experience and suspense can help us here.

## Using Suspense

### Strict Mode

We need to enable `strict mode` and make sure our application passes it. Strict mode is a wrapper that we can apply at the top level of the application. It makes sure that we are not using any deprecated APIs:

```jsx
import React from "react"
import ReactDOM from "react-dom"
import App from "./Root"
import "./index.scss"

ReactDOM.render(
  <React.Strict>
    <App />
  <React.Strict>,
  document.getElementById('root'))
);
```

You can use `React.Strict` in production. There is no downside to use it there.

### Concurrent Mode

We can start using suspense without enabling concurrent mode but in order for React to wait and pause rendering we need to enable it.

```jsx
import React from "react"
import ReactDOM from "react-dom"
import App from "./Root"
import "./index.scss"

ReactDOM.createRoot(
  document.getElementById('root')).render(
    <React.Strict>
      <App />
    <React.Strict>
  );
```

Once we have concurrent mode enabled we can wrap our app in `React.Suspense`:

```jsx
render() {
  <React.Suspense fallback={<Spinner />}>
    <Router>
      <Nav>
        <HomePage path="/" />
        <ArtistPage path="/artist/:id" />
      </Nav>
    </Router>
  </React.Suspense>
}
```

### Lazy Loading

Most React apps will have their files in bundles. Essentially there will be one big minified javascript file that the browser will process to display your application to the user. But with time your app tends to grow, so inevitably the bundle size also grows. You might end up in a situation where it takes really long time for your app to render. This is not so good.

To avoid winding up a large bundle, it's good to get ahead of the problem and start 'splitting' your bundle. By doing so you can now `lazy-load` only the things the user is using. So in result the app size will not be smaller, but it can dramatically improve the performance. Now your app doesn't even render the things that user doesn't need.

The `React.lazy` function lets you render a dynamic import as a regular component. Although `React.lazy` and `Suspense` is not available for server-side rendering. If you want to do code-splitting in a server rendered app, you can use `Loadable Components`.

A good place to use code splitting is the navigation.

```jsx
const HomePage = React.lazy(() => import('./components/HomePage'))
const ArtistPage = React.lazy(() => import('./components/ArtistPage'))

render() {
  <React.Suspense fallback={<Spinner />}>
    <Router>
      <Nav>
        <HomePage path="/" />
        <ArtistPage path="/artist/:id" />
      </Nav>
    </Router>
  </React.Suspense>
}
```

`React.lazy` takes a function that must call a dynamic `import()`. This must return a Promise which resolves to a module with a default export containing a React component.

### Use Suspense for assets

Lets say we want to display an image to the user. We hit the server with the request and while the user is waiting for image to appear - we simply show a spinner. On a slow connection this user experience is not ideal. It would be better if we could show something like optimised image for bad connections.

One technique that is popular is to use low quality image placeholder. The idea is that you take a smaller version of the same image and load that as fast as possible. In the background you load the high resolution version. Once the high resolution image comes from the sever - you swap them around. This is kind of still like showing the spinner. It's just we are using image instead.

With `React Suspense` we can let React to figure out wheather it should stop what it's doing and wait until that high resolution image has been downloaded and just show the later or if it should fallback. This leads to a much better user experience as well as very nice developer experience.

First we need to create `resource` from React cache. It is going to store anything that is asynchronous and can resolve to a value. `react-cache` will allow us to access that in a synchronous manner.

```jsx
import { unstable_createResource } from "react-cache"

const ImageResource = unstable_createResource(
  source =>
    new Promise(resolve => {
      const img = new Image()
      img.src = source
      img.onload = resolve
    })
)
```

Let's create our own `Img` image component. This is going to take same props as a normal `img` component. The we are going to read from the cache and return image. When `Img` renders and gets to `ImageResource.read(src)` part - it's going to find nereast `Suspense` parent and React is going to pause at that parent and make a decision - either render the fallback and continue down the tree or it's going to wait long enough for data to be available and provide to the component.

```jsx
const Img = ({ src, alt, ...props }) => {
  ImageResource.read(src)
  return <img src={src} alt={alt} {...props} />
}

function ArtistHeader({ artist }) {
  return (
    <div>
      <React.Suspense
        /* amount of time to wait until rendering fallback */
        maxDuration={500}
        fallback={
          <img
            className="artist-image preview"
            /* low quality version */
            src={artist.images[2].url}
            alt={artist.name}
          />
        }
      >
        <Img
          className="artist-image loaded"
          src={artist.images[0].url}
          alt={artist.name}
        />
      </React.Suspense>
    </div>
  )
}
```

## Benefits of Suspense

It feels "sync". When you create resource and you read from the cache it reached from top to bottom. So in result you look at the `render()` function and understand what is going on.

It is easy coordinate. Before you might end up managing quite a few loading states throughout your app. With `Suspense` it's declarative and easy to reason about. It helps you get to fast & prety app balance.
