`Async` and `Await` are two keywords that can help make async code read more like sync code.

For example, the two code blocks below do the exact same thing. They both get information from a server, process it, and return a promise.

```Js
function getPersonsInfo(name) {
	return server.getPeople().then(people => {
	return people.find(people => {return person.name === name});
	});
}
```

```Js
async function getPersonsInfo(name) {
	const people = await server.getPeople();
	const person = people.find(people => {return person.name === name});
	return person;
}
```

The second example looks much more like the kind of functions you are used to writing. However, did you notice the `async` keyword before the function declaration? How about the `await` keyword before `server.getPeople()`?

### The async keyword

The `async` keyword is what lets the JavaScript engine know that you are declaring an asynchronous function. This is required to use `await` inside any function. When a function is declared with `async`, it automatically returns a promise; returning in an `async` function is the same as resolving a promise. Likewise, throwing an error will reject the promise.

An important thing to understand is `async` functions are just syntactical sugar for `promises`.

It's valid to use an `async` function anywhere you can use a normal function.

```javascript
const yourAsyncFunction = async () => {
  // do something asynchronously and return a promise
  return result;
}
```

```javascript
anArray.forEach(async item => {
  // do something asynchronously for each item in 'anArray'
  // one could also use .map here to return an array of promises to use with 'Promise.all()'
});
```

```javascript
server.getPeople().then(async people => {
  people.forEach(person => {
    // do something asynchronously for each person
  });
});
```

### The await keyword

`await` does the following: it tells JavaScript to wait for an asynchronous action to finish before continuing the function. It’s like a ‘pause until done’ keyword. The `await` keyword is used to get a value from a function where you would normally use `.then()`. Instead of calling `.then()` after the asynchronous function, you would assign a variable to the result using `await`.

### Error handling

Handling errors in `async` functions is very easy. Promises have the `.catch()` method for handling rejected promises, and since async functions just return a promise, you can call the function, and append a `.catch()` method to the end.

```javascript
asyncFunctionCall().catch(err => {
  console.error(err)
});
```

But there is another way: the mighty **try/catch** block! If you want to handle the error directly inside the async function, you can use `try/catch` with `async/await` syntax. If JavaScript throws an error in the `try` block, the `catch` block code will run instead (this can also be used for synchronous code).

```javascript
async function getPersonsInfo(name) {
  try {
    const people = await server.getPeople();
    const person = people.find(person => { return person.name === name });
    return person;
  } catch (error) {
    // Handle the error any way you'd like
  }
}
```
