---
title: React design patterns
date: "2015-05-06T23:46:37.121Z"
---

Design pattern - is a solution to recurring problems. I like to think of design patterns as a recipes that help us cook delicious react apps. I hope this blog post will add new recipes to your cook book.

A disclaimer - I didn't invent these recipes myself. I rather gathered them from the community.

3 recipes that I'm going to introduce here are:

- Context API
- Presentation/Container Components
- Render Props

Cookbook Recipe #1: Context API
This is a recipe championed by Kent C Dodds. At least I heard about it from him first. This API was part of React a long time ago, however a new version came out of it and that was the time when Kent reintroduced the idea of using the context API.

Passing props can become very annoying and frustrating once you are passing them more than 1 level deep. Well, maybe 2 level deep is still somewhat manageble but what if you end up in a situation like passing props 4 level deep (1.jpg) - you pass it through a side bar, through settings into a reset password component. So we're passing this prop down multiple layers inside of our app. This is a problematic because we end up passing this prop through a bunch of intermediary components that don't need that information. It's also possible that through this journey during this time as it moves between all of these props sometimes it changes name, for some reason the developer decides that name needs a change and so later when you come back and you try and find the origin of this prop you have like four or five files open as you're trying to track it all the way back up the chain. When Kent C Dodds mentions about this problem he referredf to it as 'prop drilling'. For people who are new to react it's something that they experience quite early on in their journey. You would usually want to find if there's an easier way to resolve this. Probably the intuitive way of finding a solution would be using a redux library. But it comes with a lot of the boilerplate code. And if you are new to this concpet then it's quite hard to get a good understanding quickly. Sometimes we want our state to be global.

Context api: "a way to pass data through the component tree without having to pass props down manually at every level." - React Documentation. This API gives us the ability to access a prop from lower down our tree without needing to pass it through intermediary components.
