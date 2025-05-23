### Why does React need keys?

In the previous lesson on rendering lists, we used the `.map()` method to iterate over an array of data and return a list of elements. Now imagine, if any of the items in the list were to change, how would React know which item to update?

If the list were to change, one of two things _should_ happen:

1. we completely re-render the entire list, or:
2. we hunt down the specific items that were changed and only re-render those.

Assuming we want to hunt down that one specific item that was changed and NOT re-render the entire list, we need something to track that specific item. We can track down a specific item by using a `key`.

As long as `keys` remain consistent and unique, React can handle the DOM effectively and efficiently.

Keys are passed into the component or a DOM element as a prop.

```jsx
<Component key={keyValue} />
//or
<div key={keyValue} />
```

Now that we know the syntax, the next question is: what should be used as a key? Ideally, there should be some identifier that is unique to each item in the list. Most databases assign a unique id to each entry, so you shouldn’t have to worry about assigning an id yourself. If you are defining data yourself, it is good practice to assign a unique `id` to each item. You can use the `crypto.randomUUID()` function to generate a unique id. Let’s look at an example:

```jsx
// a list of todos, each todo object has a task and an id
const todos = [
  { task: "mow the yard", id: crypto.randomUUID() },
  { task: "Work on Odin Projects", id: crypto.randomUUID() },
  { task: "feed the cat", id: crypto.randomUUID() },
];

function TodoList() {
  return (
    <ul>
      {todos.map((todo) => (
        // here we are using the already generated id as the key.
        <li key={todo.id}>{todo.task}</li>
      ))}
    </ul>
  );
}
```

Additionally, if you’re sure the list will remain unchanged throughout the application’s life, you can use the array index as a key. However, this is not recommended since it can lead to confusing bugs if the list changes when items are deleted, inserted, or rearranged.

```jsx
const months = ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'];

function MonthList() {
  return (
    <ul>
      {/* here we are using the index as key */}
      {months.map((month, index) => (<li key={index}>{month}</li>))}
    </ul>
  );
}
```

Keys are straightforward to use, though there is an anti-pattern you should be aware of. Keys should never be generated on the fly. Using `key={Math.random()}` or `key={crypto.randomUUID()}` _while_ rendering the list defeats the purpose of the key, as now a new `key` will get created for every render of the list. As shown in the above example, `key` should be inferred from the data itself.

```jsx
// Do the following
const todos = [
  { task: "mow the yard", id: crypto.randomUUID() },
  { task: "Work on Odin Projects", id: crypto.randomUUID() },
  { task: "feed the cat", id: crypto.randomUUID() },
];

function TodoList() {
  return (
    <ul>
      {todos.map((todo) => (
        // DON'T do the following 
        // i.e. generating keys during render
        <li key={crypto.randomUUID()}>{todo.task}</li>
      ))}
    </ul>
  );
}
```