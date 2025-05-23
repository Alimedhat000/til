###  Breakdown of MVC:

#### Model
- The **data layer**.
- Handles the logic for **data**, **database communication**, and **business rules**.
- Example: a `User` model that fetches or saves users to a database.
#### View
- The **presentation layer** (what the user sees).
- Responsible for rendering HTML, templates, or frontend code.    
- Example: an HTML file or a template like `EJS`, `Pug`, or `Handlebars`.

#### Controller
- The **logic layer** between the Model and the View.
- Handles **user input**, **processes requests**, fetches/updates data via the Model, and returns a response via the View.
- Example: a function that gets form data, saves it using the model, and renders a success view.

### Handling Responses

When it comes to sending responses from our controllers, we have several methods at our disposal. Let’s explore some of the commonly used methods and their use cases.

- [res.send](https://expressjs.com/en/api.html#res.send) - A general-purpose method for sending a response, it is flexible with what data we can send since it will automatically set the `Content-Type` header based on what data you pass it. For example, if we pass in an object, it will stringify it as JSON and set the `Content-Type` header to `application/json`.

- [res.json](https://expressjs.com/en/api.html#res.json) - This is a more explicit way to respond to a request with JSON. This always sets the `Content-Type` header to `application/json` and sends the data as JSON.

- [res.redirect](https://expressjs.com/en/api.html#res.redirect) - When we want to redirect the client to a different URL, this method allows for that capability.

- [res.render](https://expressjs.com/en/api.html#res.render) - `res.render` lets you render a view template and send the resulting HTML as the response. We’ll cover this in a later lesson.

There is also a useful method that we can use to set the status code manually.

- [res.status](https://expressjs.com/en/api.html#res.status) - This sets the response’s status code **but does not end the request-response cycle by itself**. We can chain other methods through this (e.g. `res.status(404).send(...)` but note that we can’t do `res.send(...).status(404)`). We can exclude this if we wish to use the default status code of `200`.

We also need to take note that these response methods only **end the request-response cycle**. They **do not end the function execution**. For example if we do this:

```javascript
app.use((req, res) => {
  // This works and this ends the request-response cycle
  res.send("Hello");

  // However, it does not exit the function so this will still run
  console.log('will still run!!');

  // This will then throw an error that we cannot send again after sending to the client already
  res.send("Bye");
});
```
#### `res.send` and `res.json`

If `res.send` automatically sets the `Content-Type` based on the data passed, why would we still use `res.json`? `res.json` enforces JSON and will automatically convert non-object values to JSON, but `res.send` will not. `res.json` is just a convenient method that also internally calls `res.send`. `res.send` will only handle things as JSON when dealing with booleans and objects (which includes arrays).

So for convenience it’s more appropriate to use `res.json` instead of `res.send`, and if we’re sending JSON, we might as well use a method that’s literally named “json”. It’s like the perfect match!

### Middleware 

Middleware functions are a core concept in Express and play a crucial role in handling requests and responses. They operate between the incoming request and the final intended route handler.

A middleware function typically takes three arguments (however, there is one that we will get into later that has four):

- `req` - The request object, representing the incoming HTTP request.
- `res` - The response object, representing the HTTP response that will be sent back to the client.
- `next` - The function that passes control to the next middleware function in the chain (we’ll get to this later). This is optional.

A middleware function can perform various tasks, such as:

- Modifying the request or response objects (some packages for example will do this, like adding a new property in the request object, or setting the `res.locals` that is used in templates rendered with `res.render`).
- Executing additional code (validation middleware functions to validate the request before going to our main logic, authentication middleware functions, and so on).
- Calling the next middleware function in the chain.
- Ending the request-response cycle (meaning no further middleware functions are called, even if there are more in the chain).
#### Application-level middleware

Application-level middleware are bound to an _instance of Express_ using `app.use` or using `app.METHOD` (e.g. `app.get`, `app.post`) functions.  Typically, these middleware functions are placed on top of our application code to ensure they always run first.

Express offers several essential built-in middleware functions that we’ll frequently use in our applications. These include:

- Body parsers (e.g. `express.json`, `express.urlencoded`) - These allow us to correctly parse the incoming request’s body, so that we can use it through `req.body`.
- Serving static files (e.g. `app.use(express.static('public'))`) - This middleware function serves static files like HTML, CSS, JavaScript, and images.

#### Router-level middleware

Router-level middleware works similarly to an application-level middleware, but it’s bound to an _instance of Express router_ using `router.use` or `router.METHOD` (e.g. `router.get`) functions. Because of this, Express only executes these middleware when the request matches and goes through that router.


Here is an example of a basic middleware function:

```javascript
function myMiddleware(req, res, next) {
  // Perform some operations
  console.log("Middleware function called");

  // Modify the request object
  req.customProperty = "Hello from myMiddleware";

  // Call the next middleware/route handler
  next();
}

app.use(myMiddleware);
```

### Controllers

They are just functions also classified as middleware (at least in the express world) that are used by route handlers. However, it should be noted that [[#Controller]] and [[#Middleware]] are distinct concepts. So we are using middleware in Express to implement the *Controller* part of the MVC pattern.

A controller comes into play whenever a request hits the server and a route matches the requested HTTP verb and path. The route determines which controller should handle the request based on the defined middleware chain. The appropriate controller then takes over, performing the necessary actions to fulfill the request. This could involve retrieving data from the model, processing the data, making decisions based on business logic, or updating the model with new data.

The naming convention for these controllers are usually based on the route they will be attached to e.g. `GET` route -> `getSomething`, `POST` route -> `createSomething`, `DELETE` route -> `deleteSomething`, etc. 

example

```javascript
// controllers/authorController.js

const db = require("../db");

async function getAuthorById(req, res) {
  const { authorId } = req.params;

  const author = await db.getAuthorById(Number(authorId));

  if (!author) {
    res.status(404).send("Author not found");
    return;
  }

  res.send(`Author Name: ${author.name}`);
};

module.exports = { getAuthorById };
```

### Handling errors

#### try/catch

Using the same code from earlier, we can quickly handle errors by wrapping our controller logic in a `try/catch` block.

```javascript
async function getAuthorById(req, res) {
  const { authorId } = req.params;

  try {
    const author = await db.getAuthorById(Number(authorId));

    if (!author) {
      res.status(404).send("Author not found");
      return;
    }

    res.send(`Author Name: ${author.name}`);
  } catch (error) {
    console.error("Error retrieving author:", error);
    res.status(500).send("Internal Server Error");

    // or we can call next(error) instead of sending a response here
    // Using `next(error)` however will only render an error page in
    // the express' default view and respond with the whole html to the client.
    // So we will need to create a special type of middleware function
    // if we want a different response and we will get to that in a bit.
  }
};
```

However, this is not exactly a clean solution since eventually, we will have to add the same try/catch block to _all_ controllers. We can install a package called [express-async-handler](https://www.npmjs.com/package/express-async-handler) to hide this bit of code.

```javascript
const asyncHandler = require("express-async-handler");

// The function will automatically catch any errors thrown and call the next function
const getAuthorById = asyncHandler(async (req, res) => {
  const { authorId } = req.params;

  const author = await db.getAuthorById(Number(authorId));

  if (!author) {
    res.status(404).send("Author not found");
    return;
  }

  res.send(`Author Name: ${author.name}`);
});
```

#### With a middleware

An error middleware function handles all errors in our application that come down from other middleware functions. We want to place this error middleware function at the very end of the application code to ensure it’s the last middleware function executed and only handles errors bubbling down from preceding middleware functions.

However, take note that this is a middleware function that requires _four parameters_ that we will need to provide even if they are not used. If for example we exclude one of the parameters, it will not be recognized as an error middleware function.

```javascript
// Every thrown error in the application or the previous middleware function calling `next` with an error as an argument will eventually go to this middleware function
app.use((err, req, res, next) => {
  console.error(err);
  res.status(500).send(err);
});
```

This middleware function handles the _errors thrown_ in other middleware functions or something that is sent by a previous middleware function using the `next` function (e.g. `next(err)`).

So the way Express distinguishes this middleware function is again through adding _four parameters_, not a single one missing. A route middleware function or a middleware function with _less than four parameters_ will always be considered as a request middleware function instead of this error middleware function, even if we place it last.

```javascript
app.use((req, res, next) => {
  throw new Error("OH NO!");
  // or next(new Error("OH NO!"));
});

app.use((err, req, res, next) => {
  console.error(err);
  // You will see an OH NO! in the page, with a status code of 500 that can be seen in the network tab of the dev tools
  res.status(500).send(err.message);
});
```

#### Creating custom errors

With the solutions above, the error middleware function can only really respond with a `500` status code no matter what error it is. But what if we actually want to send a `404`?

Create the following file `CustomNotFoundError.js` within an `errors` folder:

```javascript
// errors/CustomNotFoundError.js

class CustomNotFoundError extends Error {
  constructor(message) {
    super(message);
    this.statusCode = 404;
    // So the error is neat when stringified. NotFoundError: message instead of Error: message
    this.name = "NotFoundError";
  }
}

module.exports = CustomNotFoundError;
```