---
layout: post
title: What style of functions do you prefer to use in React and why?
slug: what-style-of-functions-do-you-prefer-to-use-in-react-and-why
date_published: 2020-04-19T20:07:13.000Z
date_updated: 2020-09-02T22:44:24.000Z
tags: React
image: /assets/images/2020-04-19-what-style-of-functions-do-you-prefer-to-use-in-react-and-why/cover.png
excerpt: When you start, there are various function styles thrown around… Which one should you use?
---

In React world, developers thrown around various function styles. For example:

```jsx
const Text1 = (props) => (
  <div>{props.message}</div>
);

const Text2 = ({ message }) => (
  <div>{message}</div>
);

function Text3(props) {
  return <div>{props.message}</div>
}

function Text4({ message }) {
  return <div>{message}</div>
}
```

Took from [reddit](https://www.reddit.com/r/reactjs/comments/fvd0h9/what_style_of_functions_do_you_prefer_to_use_in/) 
Which one is better? Which one should you use?

Quick answer: the four are equivalent.

As stated in this [discussion on Twitter](https://twitter.com/dan_abramov/status/996710899921686528), at the end of the day it doesn't matter and **it is more of a personal preference.**

> It so doesn’t matter
> &mdash; Dan Abramov (@dan_abramov) [May 16, 2018](https://twitter.com/dan_abramov/status/996710899921686528?ref_src=twsrc%5Etfw)

What is important is to **stick with one** when building an application. This way:

- Your code will be easier to read because it use the same convention everywhere
- You will focus on building the app instead of thinking about how to write your components.
- You won't confuse the person who read the code (could be you!) by writing similar code in different ways.

## My personal preference

I use named functions with [destructed props](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) to define my components:

```jsx
function Text4({ message }) {
  return <div>{message}</div>
}
```

My personal preference: named function with destructured props

Why this style? Let's compare with alternatives:

### function vs const

When using a const, I read it as “*I'm defining an anonymous function and storing it in a variable*”. While with a function it's like “*here is a function*”.

It is longer to type but it is easier to read.

### Destructed props

What I like about destructed props is that I know what are the accepted props *as I read the component.* I know this is not a strong argument, since there are other ways to do it. But this is the easier 😇

### How to be consistent without thinking about it

> *“I want to write code. Not spend cycles on formatting.”*

There are tools that help to maintain the same code convention in our code 🥰 

In JavaScript world, the most popular is [Prettier](https://prettier.io) which is an *opinionated code formatter.* It means that when you click “save”, it updates your code to follow certain rules 🧙
