---
layout: post
title: From class component to hooks (Cheatsheet included)
slug: from-class-components-to-hooks
date_published: 2020-08-03T23:53:24.000Z
date_updated: 2020-09-02T22:49:57.000Z
tags: React
excerpt: Refactoring a small class component to hooks
---

You are comfortable with class components and you are wondering if you should learn React hooks (short answer: *yes, you should*).

Don't worry about losing your job if you don't learn hooks because class components [won't be deprecated](https://reactjs.org/docs/hooks-faq.html#do-i-need-to-rewrite-all-my-class-components) and there is no need to update all codebases to hooks.

However, **hooks are a thing**. They may look over-hyped, but they are important specially when starting something from scratch.

I know that feeling when you're comfortable with a technology and you've to learn a new toy (I had to learn Node when I was used to Ruby on Rails 😭)
![](/content/images/2020/08/imagen.png)You deciding to learn hooks
In this post we are going to use what you already know to kick-off your hooks journey. I know that your brain works well with classes, so lets take this advantage!

## Refactoring a simple component

One of the first things we learn in React (classes) is the [component lifecycle](https://reactjs.org/docs/react-component.html#the-component-lifecycle): **constructor**, **componentDidMount**, **componentWillUnmount **and **render**.

Let's use them and create a [simple counter example](https://1toqw.csb.app/):

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
    

See demo in [codesandbox](https://codesandbox.io/s/async-shadow-1toqw?file=/src/App.js)
Nothing new until this point:

- When the component is initialized, we create the state with `counter = 0`
- After the component is mounted, we start a timer interval that increases the counter by one every 1000 milliseconds (1 second).
- Before the component is unmounted, we clear the timeout.

How we refactor it with hooks?

## Step 1: transform your class component into function component

In other words:

1. Replace *class* for *function*
2. Remove extends *React.Component*
3. Remove everything inside except the *render* code

In our case:

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

This is not working (yet)
## Step 2: state management with useState

You know how to use state in class components:

- Initialize state in the constructor.
- Update it using *setState* method which triggers a new render.
- Read state properties from *this.state*

In functional components, some concepts are similar: state keeps values between renders and updating state triggers a new render. But the way it works is a bit different.

To use state in functional components, we have to [useState hook](https://reactjs.org/docs/hooks-state.html):

    export default function FunctionalComponentWithState() {
        const someState = React.useState(initialValue);
        // ...
    }

Using useState hook
By calling *useState*, [you are creating a single piece of state](https://daveceddia.com/usestate-hook-examples/) associated to the component. So it is like initializing the state in the constructor of a class component.

*useState* returns two things: 1) the current value of the state variable and 2) a function to update it:

    function ComponentWithState() {
        const [currentValue, updateValue] = React.useState(initialValue);
    }
    

Creating a piece of state, getting the current value and a function to update it. Magic! 🤯
In the counter example, we need to store the counter. So instead of

    const { counter } = this.state;

Reading state in class components
We should use:

    const [counter, setCounter] = useState(0); // 0 is the initial value
    

In one line we tell react to create a state variable with an initial value, get the current value and a function to update it.
Now our component looks like:

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
    

Functional component with state
[This works](https://codesandbox.io/s/weathered-sun-fdgeq)! But we are not done yet because we are not counting in every second.

## Step 3: implement componentDidMount with useEffect hook

Instead of *componentDidMount*, we have to use [useEffect hook](https://reactjs.org/docs/hooks-effect.html).

The *useEffect* hook takes a function which will be invoked after render. Similar to *componentDidMount*:

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
    

Where to add "componentDidMount" logic
After *render (inside useEffect)*, we have to:

- start the timer (setTimeout)
- update the counter on every tick

Instead of using *setState* we should use *setCounter* (from useState). So the state update logic:

    this.setState(state => ({
      counter: state.counter + 1
    }));
    

Update class state
is replaced with:

    setCounter(value => value + 1);
    

Update hook state
When *setCounter* is called, **it will trigger a new render** (similar to *this.setState* in the class component).

Now our component looks like:

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
    

This does almost everything =)
> I said before that the *function you pass to useEffect is invoked **after render***. That is different than *"after mount"*. **The empty array is used to tell React to invoke only once **(on mount!)

    useEffect(() => {
      setInterval(() => {
        setCounter(oldCounterValue => oldCounterValue + 1)
      }, 1000);
    }, []); // <--- HERE
    

useEffect that runs only once
## Step 4: implement componentWillUnmount with useEffect cleanup function

*useEffect* can return a function that [cleans up effects](https://reactjs.org/docs/hooks-effect.html#effects-with-cleanup) from the previous render, which can be used to remove subscriptions (for example, to call `clearInterval`).

If your effect runs only on *"mount"* (like our component), the cleanup logic will run only on *"unmount". *Our updated useEffect looks like this:

    useEffect(() => {
      cont intervalId = setInterval(() => {
        setCounter(oldCounterValue => oldCounterValue + 1)
      }, 1000);
      
      return () => { // <--- returns a "cleanup" function
        // "on unmount"
        clearInterval(intervalId);
      }
      
    }, []);
    

useEffect with "on unmount" logic
## The final code

And the final code:

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
    

See in [codesandbox](https://codesandbox.io/s/weathered-sun-fdgeq)
## Recap

### **useState** instead of **this.state = { ... }**

**useState** asks for an initial value, and returns the current value and a function to update it. All in a single line of code:

    const [currentValue, setValue] = useState(initialValue);
    

Component state management with useState hook
Calling **setValue** will trigger a new render, just like using **setState**.

### **useEffect** instead of **componentDidMount** / **componentWillUnmount**

**useEffect** takes a function which is invoked after render. This is generally used to run code with side effects such as HTTP requests, subscriptions, etc. Similar to what you do in **componentDidMount**

You can tell react to run *cleanup logic* (removing a subscription for example) that you added in your effect by making your effect return a function.

    useEffect(() => {
      // on mount: do something
      return () => {
        // on unmount: do something
      }
    }, []); // <--- empty array = tell react to run it once
    

*useEffect* that imitates *componentDidMount* and *componentWillUnmount*
---

Too much to remember? Don't worry, I made a *"Class components to Hooks"* Cheatsheet you can use 🎁 💝 Get it below👇
