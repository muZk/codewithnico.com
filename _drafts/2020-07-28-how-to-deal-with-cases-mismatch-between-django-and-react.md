---
layout: post
title: How to manage snake case for backend (Django, Rails) and camel case for React?
slug: how-to-deal-with-cases-mismatch-between-django-and-react
date_published: 2020-07-28T16:43:51.000Z
date_updated: 2020-09-07T18:40:42.000Z
tags: React
---

Let say that your backend framework is Django or Rails. The data you get and send must be in snake_case because this is the convention in these frameworks.

But when handling this data in your React App, you'll be using JavaScript where the convention is camelCase...

How can you solve this without making your Linter go mad? Should you break the convention for either Rails or React?

If you don't mind breaking name conventions, go ahead and use snake case in your React App (sorry Linter! 💔)

For the rest of us, I have a solution:

- *after* retrieving data from your backend, transform it to camelCase
- *before* sending data to your backend, transform it to snake_case

Why in client code you might ask? Well, because 99% of the time you will be adding somekind of *fetch* wrapper in your app (for example, to transform the responses to JSON). Just extend it a little.

## Show me the code

The transformation is easy because there are packages for it. I'll be using [humps](https://www.npmjs.com/package/humps), which is a Underscore-to-camelCase converter (and vice versa) for strings and object keys in JavaScript.

> *after* retrieving data from your backend, transform it to camelCase

Basically:

    // api.js
    
    import humps from 'humps';
    
    export async function get(url) {
        return fetch(url)
            .then(response => response.json())
            .then(json => humps.camelizeKeys(json))
    }
    

get wrapper
> before sending data to your backend, transform it to snakeCase

    // api.js
    
    import humps from 'humps';
    
    export async function post(url, data) {
        const body = JSON.stringify(humps.decamelizeKeys(data));
        return fetch(url, { method: 'POST', body })
            .then(response => response.json())
            .then(json => humps.camelizeKeys(json))
    }

post wrapper
Then in your React components use your new functions and you're done!

### Example
![](/content/images/2020/09/Screen-Shot-2020-09-07-at-15.39.27.png)[Demo](https://codesandbox.io/s/distracted-wilbur-zr914?file=/src/App.js)
There is the code of [example App](https://codesandbox.io/s/distracted-wilbur-zr914?file=/src/App.js) using [PokeApi](https://pokeapi.co/docs/v2):

{% raw %}
```jsx
import React, { useState, useEffect } from "react";
import "./styles.css";
import { get } from "./api";

function PokemonCard({ name }) {
  const [pokemon, setPokemon] = useState(null);

  useEffect(() => {
    get(`/pokemon/${name}`).then(data => setPokemon(data));
  }, [name]);

  if (!pokemon) return null;

  const src = pokemon.sprites.frontDefault; // camelCase :D

  return (
    <div
      style={{
        margin: 10,
        width: 96,
        height: 96,
        display: "inline-block",
        backgroundImage: `url(${src})`
      }}
    />
  );
}

export default function App() {
  const [pokemons, setPokemons] = useState([]);

  useEffect(() => {
    get("/pokemon?limit=150").then(data => setPokemons(data.results));
  }, []);

  return (
    <div className="App">
      <h1>Pokemons!</h1>
      <div>
        {pokemons.map(({ name }) => (
          <PokemonCard key={name} name={name} />
        ))}
      </div>
    </div>
  );
}
```
{% endraw %}

Example App using our API wrapper
## Why it works

- Most React apps needs some-kind of wrapper for requests. It could be to transform all requests to JSON, to add authentication tokens, etc. So extending the wrapper a little for transformations is okay and straightforward.
- Sometimes you won't be able to touch your backend code. In this case, any transformation should be done in client (React) anyway.
