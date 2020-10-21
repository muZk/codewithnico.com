---
layout: post
title: From class component to hooks (Cheatsheet included)
slug: from-class-components-to-hooks
date_published: 2020-08-03T23:53:24.000Z
date_updated: 2020-09-02T22:49:57.000Z
tags: React
image: /assets/images/2020-08-03-from-class-components-to-hooks/cover.png
excerpt: Refactoring a small class component to hooks
---

You are comfortable with class components and you are wondering if you should learn React hooks (short answer: *yes, you should*).

Don't worry about losing your job if you don't learn hooks because class components [won't be deprecated](https://reactjs.org/docs/hooks-faq.html#do-i-need-to-rewrite-all-my-class-components) and there is no need to update all codebases to hooks.

However, **hooks are a thing**. They may look over-hyped, but they are important specially when starting something from scratch.

I know that feeling when you're comfortable with a technology and you've to learn a new toy (I had to learn Node when I was used to Ruby on Rails 😭)

![Ah shit, here we go again](/assets/images/2020-08-03-from-class-components-to-hooks/ah-shit.png)
<figcaption>You deciding to learn hooks</figcaption>

In this post we are going to use what you already know to kick-off your hooks journey. I know that your brain works well with classes, so lets take this advantage!

## Refactoring a simple component

One of the first things we learn in React (classes) is the [component lifecycle](https://reactjs.org/docs/react-component.html#the-component-lifecycle): `constructor`, `componentDidMount`, `componentWillUnmoun` and `render`.

Let's use them and create a ["you have been X seconds here"](https://1toqw.csb.app/) example.

This app will show how long have you been in the page. To build this, we will:

1. When the component is initialized, we initialize a state with `counter = 0`. We do this in the `constructor`.
2. After the component is mounted, we start a timer interval that increases the counter by one every 1000 milliseconds (1 second). We do this in `componentDidMount`.
3. Before the component is unmounted, we clear the timeout. We do this in `componentWillUnmount`.

Here is the implementation of this app:

{% raw %}
```jsx
import React from "react";

export default class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = { counter: 0 };
  }

  componentDidMount() {
    this.intervalID = setInterval(() => {
      this.setState(state => ({
        counter: state.counter + 1
      }));
    }, 1000);
  }

  componentWillUnmount() {
    clearInterval(this.intervalID);
  }

  render() {
    const { counter } = this.state;
    return (
      <div style={{ textAlign: "center" }}>
        <h1>Hey</h1>
        <div>You have been there for {counter} seconds.</div>
      </div>
    );
  }
}
```
{% endraw %}
<figcaption>
  <a href="https://codesandbox.io/s/async-shadow-1toqw?file=/src/App.js" target="_blank">Demo</a>
</figcaption>

I hope you don't see anything strange here. It uses class component with the classic component lifecycle methods.

Now I'll show you how to refactor it to hooks.

## Step 1: transform your class component into a function component

In other words:

1. Replace `class` for `function`
2. Remove `extends React.Component`
3. Remove everything inside except the `render` code

In our case:

{% raw %}
```jsx
import React from "react";

export default function App() {
  const { counter } = this.state;
  return (
    <div style={{ textAlign: "center" }}>
      <h1>Hey</h1>
      <div>You have been there for {counter} seconds.</div>
    </div>
  );
}
```
{% endraw %}

This is not working yet. We have to modify it a bit...

## Step 2: state management with useState

You know how to use state in class components:

- Initialize `state` in the `constructor`.
- Update it using `setState` method which triggers a new render.
- Read state properties from `this.state`

In functional components, the `state` works similar:

- it keeps values between renders
- updating it triggers a new render

However, in function components, you don't have a `constructor` and you don't have instance methods such as `setState`.

So the mechanics here are a bit different. To use `state` in a functional component, you have to use a **hook**, which is a function that gives superpowers to your functional components.

The hook that brings `state` superpower to our function components is called [useState hook](https://reactjs.org/docs/hooks-state.html)

```jsx
export default function FunctionalComponentWithState() {
  const someState = React.useState(initialValue); // <-- state superpower!!
  // ...
}
```
<figcaption>Using useState hook</figcaption>

By calling *useState*, [you are creating a single piece of state](https://daveceddia.com/usestate-hook-examples/) associated to the component. It is similar to initializing the state in the constructor of a class component.

`useState` returns two things:

1. the current value of the state variable
2. a function to update it:

```jsx
function ComponentWithState() {
  const [currentValue, updateValue] = React.useState(initialValue);
}
```
<figcaption>Creating a piece of state, getting the current value and a function to update it. Magic! 🤯</figcaption>

> Well, technically `useState` returns an array of size two where the first element is the current value and the second element is a function to update it.

Lets go back to the example. We need to store the counter, so instead of

```js
  const { counter } = this.state;
```
<figcaption>Reading state in a class component</figcaption>

We should use:

```js
const [counter, setCounter] = useState(0); // 0 is the initial value
```
<figcaption>In one line we tell react to create a state variable with an initial value, get the current value and a function to update it.</figcaption>

Now our `App` code should look like this:

{% raw %}
```jsx
import React, { useState } from "react";
    
export default function App() {
  const [counter, setCounter] = useState(0);
  return (
    <div style={{ textAlign: "center" }}>
      <h1>Hey</h1>
      <div>You have been there for {counter} seconds.</div>
    </div>
  );
}
```
{% endraw %}
<figcaption>Functional component with state</figcaption>

[This works!](https://codesandbox.io/s/weathered-sun-fdgeq) But we are not done yet because we are not counting every second.

## Step 3: implement componentDidMount with useEffect hook

In function components, to execute code after the component has been rendered you have to use another superpower (hook) called [useEffect hook](https://reactjs.org/docs/hooks-effect.html).

The `useEffect` hook takes a function which will be invoked after render. Very similar to `componentDidMount`:

{% raw %}
```jsx
import React, { useState, useEffect } from "react";

export default function App() {
  const [counter, setCounter] = useState(0);
  
  useEffect(() => {
    // "componentDidMount" logic should be here
  }, []);
  
  return (
    <div style={{ textAlign: "center" }}>
      <h1>Hey</h1>
      <div>You have been there for {counter} seconds.</div>
    </div>
  );
}
```
{% endraw %}
<figcaption>Adding <code>componentDidMount</code> logic</figcaption>

After `render` (in our `useEffect`) we have to:

1. start the timer (`setTimeout`).
2. update the counter on "every tick".

Instead of using `setState` we should use `setCounter`. The state update logic:

```jsx
this.setState(state => ({
  counter: state.counter + 1
}));
```
<figcaption>Code to update state in class components</figcaption>

is replaced with:

```js
setCounter(value => value + 1);
```
<figcaption>Update state with hooks</figcaption>

When `setCounter` is called, **it will trigger a new render**, similar to calling `this.setState` in the class component.

Now our App looks like this:

{% raw %}
```jsx
import React, { useState, useEffect } from "react";

export default function App() {
  const [counter, setCounter] = useState(0);
  
  useEffect(() => {
    setInterval(() => {
      setCounter(oldCounterValue => oldCounterValue + 1)
    }, 1000);
  }, []);
  
  return (
    <div style={{ textAlign: "center" }}>
      <h1>Hey</h1>
      <div>You have been there for {counter} seconds.</div>
    </div>
  );
}
```
{% endraw %}

Which does almost everything!

> I said before that the *function you pass to useEffect is invoked after render*. That is different than *after mount*. **The empty array is used to tell React to invoke only once** (on mount!)

```jsx
useEffect(() => {
  setInterval(() => {
    setCounter(oldCounterValue => oldCounterValue + 1)
  }, 1000);
}, []); // <--- HERE
```
<figcaption>useEffect that runs only once</figcaption>

## Step 4: implement `componentWillUnmount` with `useEffect`'s cleanup function

`useEffect` can return a function that [cleans up effects](https://reactjs.org/docs/hooks-effect.html#effects-with-cleanup) from the previous render, which can be used to remove subscriptions (for example, to call `clearInterval`).

If your effect runs only on *"mount"* (like our component), the cleanup logic will run only on *unmount*. Our updated `useEffect` looks like this:

```js
useEffect(() => {
  cont intervalId = setInterval(() => {
    setCounter(oldCounterValue => oldCounterValue + 1)
  }, 1000);
  
  return () => { // <--- returns a "cleanup" function
    // "on unmount"
    clearInterval(intervalId);
  }
  
}, []);
```
<figcaption>useEffect with "on unmount" logic</figcaption>

## The final code

And the final code:

{% raw %}
```jsx
import React, { useState, useEffect } from "react";

export default function App() {
  const [counter, setCounter] = useState(0);
  
  useEffect(() => {
    const intervalId = setInterval(() => {
      setCounter(oldCounterValue => oldCounterValue + 1)
    }, 1000);

    return () => clearInterval(intervalId);
  }, []);
  
  return (
    <div style={{ textAlign: "center" }}>
      <h1>Hey</h1>
      <div>You have been there for {counter} seconds.</div>
    </div>
  );
}
```
{% endraw %}
<figcaption>
  See in <a href="https://codesandbox.io/s/weathered-sun-fdgeq" target="_blank">codesandbox</a>
</figcaption>

## Recap

### *useState* instead of *this.state = { ... }*

`useState` asks for an initial value, and returns the current value and a function to update it. All in a single line of code:

```js
const [currentValue, setValue] = useState(initialValue);
```
<figcaption>Component state management with useState hook</figcaption>

Calling `setValue` will trigger a new render, just like using `this.setState`.

### *useEffect* instead of *componentDidMount* / *componentWillUnmount*

`useEffect` takes a function which is invoked after render. This is generally used to run code with side effects such as HTTP requests, subscriptions, etc. Similar to what you do in `componentDidMount`.

You can tell react to run *cleanup logic* (removing a subscription for example) that you added in your effect by making your effect return a function.

```js
useEffect(() => {
  // on mount: do something
  return () => {
    // on unmount: do something
  }
}, []); // <--- empty array = tell react to run it once
```

<figcaption><code>useEffect</code> that imitates <code>componentDidMount</code> and <code>componentWillUnmount</code></figcaption>
