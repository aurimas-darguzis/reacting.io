---
title: React useRef hook
date: "2020-11-02T23:46:37.121Z"
---

If you want to add state to your component that persists across renders but doesn’t trigger a re-render when it’s updated, go with `useRef`. It accepts an initial value as its first argument and it returns an object that has a `current` property. Anything you add to `current` will be persisted across rerenders.

The most popular use case for `useRef` is getting access to the DOM nodes. If you pass the value you get from `useRef` as a `ref` prop on any React element, React will set the `current` property to the correspoinding DOM node. This allows us to do things like grab input values or set focus.

According to official React docs: The `useRef` hook is a function that returns a mutable `ref` object whose `.current` property is initialized with an argument, (`initialValue`). The returned object will persist for the full lifetime of the component.

```js
const refContainer = useRef(initialValue);
```

The `useRef` hook can be used in two cases: accessing DOM nodes/elements and storing mutable information.

## Accessing DOM nodes/elements

To reference DOM nodes in react, we use `ref` attribute. 

```js
<div ref={containerRef}>Sample parent container</div>
<WidgetContainer ref={widgetContainerRef} />
```

In React, the `ref` attribute will allow us to reference that element and provide us access to its methods. Below is an example of `ref` and `useRef` in a functional component. Let's enforce on component render that the input is focused, so the user can start typing. For this - we are going to use `useEffect` and pass empty dependency array, so it will run only on the time when component is being rendered.

```js
import React, { useRef, useEffect } from 'react';

const EditableInput = () => {
    const textInputRef = useRef(null);

    useEffect(() => {
      textInputRef.current.focus();
    }, [])

    return (
        <>
          <input type="text" ref={textInputRef} />
        </>
    )
}
```

## Storing Mutable Information

The `useRef` hook isn't just utilized for DOM refs. The ref object is a generic container whose `.current` property is mutable and can hold any value.

In a React component, the are two ways to keep data between re-renders:
* In a `state` variable where updating the state will cause a re-render of the component
* In a `ref` where mutating the `.current` property will not cause a re-render

What they have in common is that both of them remember their data after any re-render. The better question is when would we actually use `useRef` over `useState`? Not everything in a React application need a re-render when storing data. 

```js
import React, {useRef, useState} from 'react'

const MessageInputComponent = () => {
 const [message, setMessage] = useState("")
 const sentMessage = useRef(0);
 
 const sendMessage = () => {
    if(sentMessage.current === 3){
      return alert("Message limit is reached")
    }
    
    sentMessage.current += 1
   //code to handle sending message
 }
 
 return(
   <div>
     <input onChange = {() => setMessage(e.target.value)} value={message}/>
     <button onClick={sendMessage}>Send</button>
   </div>
 )
}

export default MessageInputComponent
```
Here we are using both `useState` and `useRef`. The message state is in charge of keeping track of what we type in the `input` tag, which would need to re-render the component to update the message displayed in the `input` field. The `sentMessage` ref is keeping track of the number of messages sent, which doesn't necessarily need to trigger re-render.

Notice how the `useRef` value was changed. It doesn't come with a set of method like `useState`. To change the value, we would directly reassign the value stored on the `.current` property of the `useRef`. As an important side note, altering a `ref` is a side effect and should be done inside of `useLayoutEffect` or the `useEffect` when using React hooks.
