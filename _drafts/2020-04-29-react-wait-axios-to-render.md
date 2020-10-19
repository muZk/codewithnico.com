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

    async render() {
      return <>{await axios.get(..)}</>
    }

await in render()
You'll get an error like this:
![](/content/images/2020/04/imagen-1.png)Error when using await in render()
Why? Because `async` operator makes the `render` function to return a `Promise`, which is an object and React doesn't know to render objects.

So, how do you make React wait for your function before render?

Well, the answer is: faking it 😏

## Waiting for Axios before rendering

There is a *fetching recipe* for doing this which is always the same:

- *Start your component in "loading mode"*
- *When your component "mounts" *do the request.
- *When the request is done*, save your data and turn off "loading mode".
- *Make sure that while your component is in "loading mode"*, display a spinner or loading text in your render function. And when not, display your data.

**How do you do this in code?**

Look at this simple React app that fetches data about a single Pokémon and displays its name and picture:
![](/content/images/2020/05/Apr-30-2020-21-55-20.gif)Example code can be found [here](https://codesandbox.io/s/wait-for-axios-before-render-in-react-h1js8?file=/src/App.js)
Here is the implementation:

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

Load, fetch and display!
How is this translated to our recipe?

### 1) Start your component in "loading mode"
![](/content/images/2020/04/Screen-Shot-2020-04-30-at-16.48.04.png)This way you start your component in "loading mode"
With the conditional rendering of *"Loading..."* text and by using useState we accomplish the "loading mode" state of the component.

> **Why are we using useState instead of a simple const? **Because we want to make App component remember this value between renders!

The first render displays *"Loading..."* in the browser.

### 2) When your component "mounts" do the request
![](/content/images/2020/04/Screen-Shot-2020-04-30-at-17.18.04.png)Mounts → "after render" → accomplished with useEffect
*useEffect* receives a function that will be called after the DOM (what the browser shows) is updated. Here we tell axios to fetch Charmander data from PokeApi.

axios.get is executed asynchronous, so nothing is changed.

### 3) When the request is done, save your data and turn off "loading mode"
![](/content/images/2020/04/Screen-Shot-2020-04-30-at-17.40.21.png)
When request is done, we update the state of our component by using `setLoading` and `setPokemon`.

Both calls update the state: 

- `setPokemon` updates `pokemon`  from `undefined` to `response.data`
- `setLoading` updates `isLoading` from `true` to `false`

So... there are 2 renders! 🤯

The second render (triggered by `setPokemon`) will do nothing in the browser because `isLoading` is `true` → component returns *"Loading..."* again.

The third render (triggered by `setLoading`) will show Charmander in the browser! Because this time `isLoading` is `false`, so we reach the end of the function were the pokemon is *finally* rendered.

**Summing-up **this confusing render stuff:
![](/content/images/2020/04/Screen-Shot-2020-04-30-at-18.33.49.png)Adding comments in key parts of the code can teach us few things :)
## Not familiar with hooks? 

Hey! Is 2020, you should! 😂

No problem. Here is the equivalent code with class component:

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

Code using class component, [codesandbox link](https://codesandbox.io/s/wait-for-axios-before-render-in-react-ulz39?file=/src/App.js)
