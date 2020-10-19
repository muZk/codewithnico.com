---
layout: post
title: What is useRef for? When should I use it?
slug: what-is-useref-for-when-should-i-use-it
date_published: 2020-09-09T20:24:22.000Z
date_updated: 2020-09-09T20:25:10.000Z
tags: React
---

If you have tried reading the docs over and over again but you still don't understand _useRef_, this post is for you!

### What is useRef?

`useRef` is like a box where you can store something for later use. A number, an object, anything you want.

![What is useRef for? When should I use it?](/assets/images/2020-09-09-what-is-useref-for-when-should-i-use-it/box.png)
<figcaption>useRef</figcaption>

The _thing_ you save in this box is _preserved_ between renders, similar to `useState`. In other words, _the box_ is not destroyed when your component is updated.

And when you change the box content, nothing happens to the component. The box content changes, but the component remains the same.

Observations:

- Unlike component state variables (`useState`), updating a ref value doesn't trigger a new render.
- Are you coming from class components? Ok, it is like storing something in an instance variable (`this.value = someValue`).

### What is it used for?

Ok, cool. `useRef` is used to store something in the component "memory" but it doesn't trigger a new render when the value is updated.

But what is it used for?

Well, there are two main use cases:

### 1) **To store DOM elements** so you can later do something with it.

For example, you can focus the password input when the user press "ENTER" on the email input:

![What is useRef for? When should I use it?](https://codewithnico.com/content/images/2020/09/no-click.gif)
<figcaption>Focus the next element when pressing "ENTER"</figcaption>

You can do it with `useRef` ([source code](https://codesandbox.io/s/nostalgic-torvalds-irewu?file=/src/App.js)):

```jsx
export default function App() {
  const emailRef = useRef();
  const passwordRef = useRef();
  return (
    <div className="App">
      <p>
        <label htmlFor="email">Email:</label>
        <input
          id="email"
          ref={emailRef}
          onKeyPress={({ charCode }) => {
            if (charCode === 13) {
              passwordRef.current.focus();
            }
          }}
        />
      </p>
      <p>
        <label htmlFor="password">Password:</label>
        <input id="password" ref={passwordRef} />
      </p>
    </div>
  );
}
```
<figcaption>useRef example to store reference to an input component</figcaption>

### 2) To store values you want to keep an eye on

Sometimes you want to keep an eye on a value but you don't need to trigger new renders when it changes.

Example: [storing the previous value of a state variable](https://reactjs.org/docs/hooks-faq.html#how-to-get-the-previous-props-or-state).

This is a simple application that display a counter and its previous values. It also has two buttons to increase the counter (by 1 and by 10):

![What is useRef for? When should I use it?](https://codewithnico.com/content/images/2020/09/Sep-09-2020-17-16-14.gif)
<figcaption>Example of useRef to store a previous value</figcaption>

[Source code](https://codesandbox.io/s/previous-value-with-useref-doh5p):

```jsx
function Counter() {
  const [count, setCount] = useState(0);
  const prevCountRef = useRef();

  useEffect(() => {
    prevCountRef.current = count;
  });

  const prevCount = prevCountRef.current;

  return (
    <div className="App">
      <h1>Previous value with useRef</h1>
      <p>
        <button onClick={() => setCount((value) => value + 1)}>
          Increase counter by 1
        </button>
        <button onClick={() => setCount((value) => value + 10)}>
          Increase counter by 10
        </button>
      </p>
      <p>
        Now: {count}, before: {prevCount}
      </p>
    </div>
  );
}

```
<figcaption>useRef to store a previous value</figcaption>
