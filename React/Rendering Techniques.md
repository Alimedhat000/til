### Rendering a list of elements in JSX

Let us say we want to create a component that lists multiple animals:

```jsx
function App() {
  return (
    <div>
      <h1>Animals: </h1>
      <ul>
        <li>Lion</li>
        <li>Cow</li>
        <li>Snake</li>
        <li>Lizard</li>
      </ul>
    </div>
  );
}
```

It is perfectly acceptable, but what if we want to render more than just four? It can be tedious and long, and most of the time, we will be dealing with a data structure (like a list) rather than hard-coding each animal. You have previously learned that we can embed expressions inside JSX with curly braces. So let us do just that:

```jsx
function App() {
  const animals = ["Lion", "Cow", "Snake", "Lizard"];

  return (
    <div>
      <h1>Animals: </h1>
      <ul>
        {animals.map((animal) => {
          return <li key={animal}>{animal}</li>;
        })}
      </ul>
    </div>
  );
}
```

We define an array called `animals`. Now inside our JSX, we use `map` to return a new array of `li` elements, adding `animal` as its text. It should now render the same as the previous snippet we wrote. This is because JSX has the ability to automatically render arrays. The following code is identical:

```jsx
function App() {
  const animals = ["Lion", "Cow", "Snake", "Lizard"];
  const animalsList = animals.map((animal) => <li key={animal}>{animal}</li>)

  return (
    <div>
      <h1>Animals: </h1>
      <ul>
        {animalsList}
      </ul>
    </div>
  );
}
```
You may be curious as to what the `key` is in our `<li>` element. We will dive into how keys work in the next lesson. But, to explain briefly, it is to let React know the identity of each element in the list. React must know this information if you are dealing with a dynamic list where you add or remove elements. Since we are only dealing with a static list, it does not matter for now.

### Rendering a list of components in JSX
	We will use `props` here, and you will learn more about them in a future lesson. For now, you just need to know that `props` are arguments that are passed into components. We will just be writing a short implementation.

```jsx
function ListItem(props) {
  return <li>{props.animal}</li>
}

function List(props) {
  return (
    <ul>
      {props.animals.map((animal) => {
        return <ListItem key={animal} animal={animal} />;
      })}
    </ul>
  );
}

function App() {
  const animals = ["Lion", "Cow", "Snake", "Lizard"];

  return (
    <div>
      <h1>Animals: </h1>
      <List animals={animals} />
    </div>
  );
}
```

We have moved our `<ul>` element to a different component called `<List />`. It still returns the `<ul>` element, but we can do a lot more with it as a component.

This component accepts a `props` which is an object containing the `animals` that we defined as a property when we wrote `<List animals={animals} />`. Do note that you can name it anything, for example, `<List animalList={animals} />`. You will still need to pass the animals to the property, but now you will use `props.animalList` instead of `props.animals`.

We have also created a different component for the `<li>` element called `<ListItem />`, which also accepts `props`, and uses `props.animal` to render the text. It should now render the same thing.

In some situations, you won’t want to render anything at all. For example, say you don’t want to show packed items at all. A component must return something. In this case, you can return `null`