---
title: React Fundamentals
date: "2020-10-12T23:46:37.121Z"
---

Frameworks will come and go, but the fundamentals will stay. It's always a good idea to bet on fundamentals. Once you understand, I mean really understand the foundation - the rest will fall in to place by itself.

Once I was a junior developer, probably I still am one, but few years in industry makes me think that now I am a strong mid-level front end engineer. But really - who can tell? Few years ago I asked my fellow javascript ninja friend - "How do you get to the level you are now?". The answer was quite simple and strighforward - "Fundamentals. Do you understand what's under the hood of the things you are using? If so, then it means you will be able to change it according to your needs, but if not - then you are driving your car with eyes closed". Mind blowing, right?

Ok, I got my answer but then what? How do I open my eyes? How do I understand how thing work "under the hood"? One way of approaching this, and it's probably the first step when starting in this career - read the documentation, get to understand the building blocks of something more complex. Another way of learning is to take something already built, take it apart, make some change and put it back together.

## Intro

React is using same APIs that we are using when it creates DOM nodes. React abstracts away the imperative browser API from us just to give much more declarative API to work with.

> Learn more about the difference between those two concepts here:
> [Imperative vs Declarative Programming](https://tylermcginnis.com/imperative-vs-declarative-programming/)

Important thing to note is that React supports more than just a web platform. It also supports native. Each of these platforms has its own code necessary for interacting with that platform.

It's not too hard to create a `Hello World` using HTML:

```html
<html>
  <body>
    <div>Hello World</div>
  </body>
  <script type="module">
    // your JavaScript here
  </script>
</html>
```

But what if we need to generate something dynamically, using JavaScript? Well, using browser APIs and just the right syntax, we could go about it like so:

```html
<body>
  <script type="module">
    const rootElement = document.createElement("div")
    document.body.append(rootElement)
    const element = document.createElement("div")
    element.textContent = "Hello World!"
    rootElement.append(element)
  </script>
  <body></body>
</body>
```

React is quite popular amongst front end development. This framework uses same API's to get the content to the DOM as you would using JavaScript (example above). We need two JavaScript files to write React applications for the web:

- React: responsible for creating react elements (kinda like
  `document.createElement()`)
- ReactDOM: responsible for render react elements to the DOM (kinda like
  `rootElement.append()`)

```html
<body>
  <div id="root"></div>
  <script src="https://unpkg.com/react@16.13.1/umd/react.development.js"></script>
  <script src="https://unpkg.com/react-dom@16.13.1/umd/react-dom.development.js"></script>
  <script type="module">
    const rootElement = document.getElementById("root")
    const element = React.createElement("div", {
      className: "container",
      children: [
        React.createElement("span", null, "Hello"),
        " ",
        React.createElement("span", null, "World"),
      ],
    })
    ReactDOM.render(element, rootElement)
  </script>
</body>
```

## Using JSX

As fun as it is to write React apps using `React.createElement(...)` it can get really messy really quick. And to be honest - it's not the most readable thing in the world. So, to solve this problem it's a common practise to use JSX (JavaSciprt XML). JSX is more intuitive than the raw React API and is easier understand when reading the code. It's fairly simple HTML-like syntactic sugar on top of the raw React APIs:

```jsx
const ui = <h1 id="greeting">Hey there</h1>

// ↓ ↓ ↓ ↓ compiles to ↓ ↓ ↓ ↓

const ui = React.createElement("h1", { id: "greeting", children: "Hey there" })
```

Because JSX is not actually JavaScript, you have to convert it using something
called a code compiler. [Babel](https://babeljs.io) is one such tool.

Now we can transform our code into this:

```html
<body>
  <div id="root"></div>
  <script src="https://unpkg.com/react@16.13.1/umd/react.development.js"></script>
  <script src="https://unpkg.com/react-dom@16.13.1/umd/react-dom.development.js"></script>
  <script src="https://unpkg.com/@babel/standalone@7.9.3/babel.js"></script>
  <script type="text/babel">
    const element = <div className="container">Hello World</div> // <--JSX
    ReactDOM.render(element, document.getElementById("root"))
  </script>
</body>
```

We can go a little bit further and show an approach which includes interpolation - insertion of something of a different nature into something else:

```html
<script type="text/babel">
  const children = "Hello World"
  const className = "container"
  const element = <div className={className}>{children}</div>
  ReactDOM.render(element, document.getElementById("root"))
</script>
```

There is also a way to pass many props to our JSX element and we can use spread operator to help us with it:

```html
<script type="text/babel">
  const children = "Hello World"
  const className = "container"
  const props = { children, className }
  const element = <div {...props} />
  ReactDOM.render(element, document.getElementById("root"))
</script>
```

### Creating custom components

Just like in regular JavaScript, we often want to share code and re-use it by using functions. If you want to share JSX, you can do that as well. In React these functions are called "components" and they have some special properties.

Let's say the DOM we want to generate is like this:

```html
<div className="container">
  <div className="message">Hello World</div>
  <div className="message">Goodbye World</div>
</div>
```

In this case, it would be cool if we could reduce the duplication for creating the React elements for this:

```html
<div className="message">{children}</div>
```

So we need to make a function which accepts the `children` as an argument and returns the React element:

```javascript
function message({ children }) {
  return <div className="message">{children}</div>
}
```

Now we can call this function with the string we want to render and generate HTML we wanted initially:

```html
<body>
  <div id="root"></div>
  <script src="https://unpkg.com/react@16.13.1/umd/react.development.js"></script>
  <script src="https://unpkg.com/react-dom@16.13.1/umd/react-dom.development.js"></script>
  <script src="https://unpkg.com/@babel/standalone@7.9.3/babel.js"></script>
  <script type="text/babel">
    // NOTE: this is NOT being used as a component. We're not quite there yet.
    function message({ children }) {
      return <div className="message">{children}</div>
    }

    const element = (
      <div className="container">
        {message({ children: "Hello World" })}
        {message({ children: "Goodbye World" })}
      </div>
    )

    ReactDOM.render(element, document.getElementById("root"))
  </script>
</body>
```

So far we've only used `React.createElement(someString)`, but the first argument to `React.createElement` can also be a function which returns something that's renderable.

Instead of calling your `message` function, pass it as the first argument to `React.createElement` and pass the `{children: 'Hello World'} object as the second argument:

```javascript
function message({ children }) {
  return <div className="message">{children}</div>
}

const element = (
  <div className="container">
    {React.createElement(message, { children: "Hello World" })}
    {React.createElement(message, { children: "Goodbye World" })}
  </div>
)

ReactDOM.render(element, document.getElementById("root"))
```

Ok, we are getting very close to create our first component. Just like using JSX for regular `div`s is nicer than using the raw `React.createElement` API, using JSX for custom components is nicer too. Don't forget - it's Babel that's responsible for taking our JSX and compiling it to `React.createElement` calls so we just need a way to tell Babel how to compile our JSX so it passes the functionby its name rather than a string.

We do this by how the JSX appears. Here are a few examples of Babel output for JSX:

```javascript
ui = <Capitalized /> // React.createElement(Capitalized)
ui = <property.access /> // React.createElement(property.access)
ui = <Property.Access /> // React.createElement(Property.Access)
ui = <Property['Access'] /> // SyntaxError
ui = <lowercase /> // React.createElement('lowercase')
ui = <kebab-case /> // React.createElement('kebab-case')
ui = <Upper-Kebab-Case /> // React.createElement('Upper-Kebab-Case')
ui = <Upper_Snake_Case /> // React.createElement(Upper_Snake_Case)
ui = <lower_snake_case /> // React.createElement('lower_snake_case')
```

Let's re-write our example but this time let's use `Message` function as the component:

```html
<body>
  <div id="root"></div>
  <script src="https://unpkg.com/react@16.13.1/umd/react.development.js"></script>
  <script src="https://unpkg.com/react-dom@16.13.1/umd/react-dom.development.js"></script>
  <script src="https://unpkg.com/@babel/standalone@7.9.3/babel.js"></script>
  <script type="text/babel">
    // This is being used as a component 🎉🎉🎉
    function Message({ children }) {
      return <div className="message">{children}</div>
    }

    const element = (
      <div className="container">
        <Message>Hello World</Message>
        <Message>Goodbye World</Message>
      </div>
    )

    ReactDOM.render(element, document.getElementById("root"))
  </script>
</body>
```

## Styling

There are two primary ways to style react components:

1. Inline styles with the `style` prop
2. Regular CSS with the `className` prop

**About the `style` prop:**

- In HTML you'd pass a string of CSS:

```html
<div style="margin-top: 20px; background-color: blue;"></div>
```

- In React, you'll pass an object of CSS:

```jsx
<div style={{ marginTop: 20, backgroundColor: "blue" }} />
```

Note that in react the `{{` and `}}` is actually a combination of a JSX
expression and an object expression. The same example above could be written
like so:

```jsx
const myStyles = {marginTop: 20, backgroundColor: 'blue'}
<div style={myStyles} />
```

Note also that the property names are `camelCased` rather than `kebab-cased`.
This matches the `style` property of DOM nodes (which is a
[`CSSStyleDeclaration`](https://developer.mozilla.org/en-US/docs/Web/API/CSSStyleDeclaration)

**About the `className` prop:**

As we discussed earlier, in HTML, you apply a class name to an element with the
`class` attribute. In JSX, you use the `className` prop.

```javascript
import React from "react"
import "../card-styles.css"

const smallCard = <div className="card card-small">small card</div>
const mediumCard = <div className="card card-medium">medium card</div>
const largeCard = <div className="card card-large">large card</div>

function App() {
    return (
        {smallCard}
        {mediumCard}
        {largeCard}
    )
}

export default App
```

And in `card-styles.css` we would have something like this:

```css
.card {
  border: 1px solid #ccc;
}
.card--large {
  width: 250px;
  height: 250px;
}
.card--medium {
  width: 150px;
  height: 150px;
}
.card--small {
  width: 50px;
  height: 50px;
}
```

These styles will apply to our components and will be rendered with the style rules defined above.

Now, knowing what you know so far from this blog post, could you make a custom `<Card />` component that accepts all the props and merges the given `style` and `className` props with the sharde values? Or even push extra more and let's say we want that our components didn't have to worry about which class name to apply for a given effect. Let's make it so it will use `size` prop and our component took care of making the card that size.

Ideally we would like to use our component like that:

```javascript
<Card size="small" style={{ backgroundColor: "grey" }}>
  small grey box
</Card>
```

If you would like to try, then don't look at the answer below, otherwise - keep reading!

That's how an implementation might look like:

```javascript
import React from 'react'
import '../card-styles.css'

function Card({style, size, className = '', ...otherProps}) {
  const sizeClassName = size ? `card--${size}` : ''
  return (
    <div
      className={`box ${className} ${sizeClassName}`}
      style={...style}
      {...otherProps}
    />
  )
}

function App() {
  return (
    <div>
      <Card size="small" style={{backgroundColor: 'grey'}}>
        small grey box
      </Card>
      <Box size="medium" style={{backgroundColor: 'green'}}>
        medium green box
      </Box>
      <Box size="large" style={{backgroundColor: 'red'}}>
        large red box
      </Box>
      <Box>sizeless box</Box>
    </div>
  )
}

export default App
```

## Rendering Arrays

One of the more tricky things with React is the requirement of a `key` prop when
you attempt to render a list of elements.

If we want to render a list like this, then there's no problem:

```jsx
const ui = (
  <ul>
    <li>One</li>
    <li>Two</li>
    <li>Three</li>
  </ul>
)
```

But rendering an array of elements is very common:

```jsx
const list = ["One", "Two", "Three"]

const ui = (
  <ul>
    {list.map(listItem => (
      <li>{listItem}</li>
    ))}
  </ul>
)
```

Those will generate the same HTML, but what it actually does is slightly
different. Let's re-write it to see that difference:

```jsx
const list = ["One", "Two", "Three"]
const listUI = list.map(listItem => <li>{listItem}</li>)
// notice that listUI is an array
const ui = <ul>{listUI}</ul>
```

So we're interpolating an array of renderable elements. This is totally
acceptable, but it has interesting implications for when things change over
time.

If you re-render that list with an added item, React doesn't really know whether
you added an item in the middle, beginning, or end. And the same goes for when
you remove an item (it doesn't know whether that happened in the middle,
beginning, or end either).

In this example, it's not a big deal, because React's best-guess is right and it
works out ok. However, if any of those React elements represent a component that
is maintaining state, that can be pretty problematic.

Nice thing about React is that this problem can be solved in a very strightforard way - we just need to add `key` property so React knows which element we are interacting with.

```jsx
const list = [
  { id: 1, label: "Orange" },
  { id: 2, label: "Apple" },
  { id: 3, label: "Banana" },
]
const listUI = list.map(listItem => <li key={listItem.id}>{listItem.label}</li>)
const ui = <ul>{listUI}</ul>
```
