---
layout: post
title: How to wait for Axios data before you render it?
slug: react-wait-axios-to-render
date_published: 2020-04-29T14:50:14.000Z
date_updated: 2020-09-02T22:45:33.000Z
tags: React
---

> Getting data with Axios is easy, but rendering it ~OMFG~

You may know how to fetch data with Axios, but how do you render it to React?

Maybe you tried something like using `await` in render function to make React wait for your request before rendering. But when you do something like:

```jsx
  async render() {
    return <>{await axios.get(..)}</>
  }
```
<figcaption>await in render()</figcaption>

You'll get an error like this:

![Error when using await in render()](/assets/images/2020-04-29-react-wait-axios-to-render/error.png)
<figcaption>Error when using await in render()</figcaption>

Why? Because `async` operator makes the `render` function to return a `Promise`, which is an object and React doesn't know to render objects.

So, how do you make React wait for your function before render?

Well, the answer is: faking it 😏

## Waiting for Axios before rendering

There is a *fetching recipe* for doing this which is always the same:

1. *Start your component in "loading mode"*
2. *When your component "mounts" *do the request.
3. *When the request is done*, save your data and turn off "loading mode".
4. *Make sure that while your component is in "loading mode"*, display a spinner or loading text in your render function. And when not, display your data.

**How do you do this in code?**

Look at this simple React app that fetches data about a single Pokémon and displays its name and picture:

![Pokémon Demo](/assets/images/2020-04-29-react-wait-axios-to-render/demo.gif)

<figcaption>Example code can be found <a href="https://codesandbox.io/s/wait-for-axios-before-render-in-react-h1js8?file=/src/App.js" target="_blank">here</a></figcaption>

Here is the implementation:

```jsx
function App() {
  const [isLoading, setLoading] = useState(true);
  const [pokemon, setPokemon] = useState();

  useEffect(() => {
    axios.get("https://pokeapi.co/api/v2/pokemon/4").then(response => {
      setPokemon(response.data);
      setLoading(false);
    });
  }, []);

  if (isLoading) {
    return <div className="App">Loading...</div>;
  }

  return (
    <div className="App">
      <h1>{pokemon.name}</h1>
      <img alt={pokemon.name} src={pokemon.sprites.front_default} />
    </div>
  );
}
```

Load, fetch and display!

How is this translated to our recipe?

### 1) Start your component in "loading mode"

![How to start your component in loading mode](/assets/images/2020-04-29-react-wait-axios-to-render/step1.png)
<figcaption>This way you start your component in "loading mode"</figcaption>

With the conditional rendering of *"Loading..."* text and by using useState we accomplish the "loading mode" state of the component.

> **Why are we using useState instead of a simple const? **Because we want to make App component remember this value between renders!

The first render displays *"Loading..."* in the browser.

### 2) When your component "mounts" do the request

![How to make a request after your component mounts](/assets/images/2020-04-29-react-wait-axios-to-render/step2.png)
<figcaption>Mounts → "after render" → accomplished with useEffect</figcaption>

*useEffect* receives a function that will be called after the DOM (what the browser shows) is updated. Here we tell axios to fetch Charmander data from PokeApi.

axios.get is executed asynchronous, so nothing is changed.

### 3) When the request is done, save your data and turn off "loading mode"

![](/assets/images/2020-04-29-react-wait-axios-to-render/step3.png)

<figcaption>When request is done, we update the state of our component by using <code>setLoading</code> and <code>setPokemon</code>.</figcaption>

Both calls update the state: 

- `setPokemon` updates `pokemon`  from `undefined` to `response.data`
- `setLoading` updates `isLoading` from `true` to `false`

So... there are 2 renders! 🤯

The second render (triggered by `setPokemon`) will do nothing in the browser because `isLoading` is `true` → component returns *"Loading..."* again.

The third render (triggered by `setLoading`) will show Charmander in the browser! Because this time `isLoading` is `false`, so we reach the end of the function were the pokemon is *finally* rendered.

**Summing-up** this confusing render stuff:

![Summary](/assets/images/2020-04-29-react-wait-axios-to-render/summary.png)
<figcaption>Adding comments in key parts of the code can teach us few things :)</figcaption>

## Not familiar with hooks? 

Hey! Is 2020, you should! 😂

No problem. Here is the equivalent code with class component:

```jsx
export default class App extends Component {
  constructor(props) {
    super(props);
    this.state = { isLoading: true, pokemon: undefined };
  }

  componentDidMount() {
    console.debug("After mount! Let's load data from API...");
    axios.get("https://pokeapi.co/api/v2/pokemon/4").then(response => {
      this.setState({ pokemon: response.data });
      this.setState({ isLoading: false });
    });
  }

  render() {
    const { isLoading, pokemon } = this.state;

    if (isLoading) {
      return <div className="App">Loading...</div>;
    }

    return (
      <div className="App">
        <h1>{pokemon.name}</h1>
        <img alt={pokemon.name} src={pokemon.sprites.front_default} />
      </div>
    );
  }
}
```
<figcaption>Code using class components in <a href="https://codesandbox.io/s/wait-for-axios-before-render-in-react-ulz39?file=/src/App.js" target="_blank">CodeSandbox</a></figcaption>
