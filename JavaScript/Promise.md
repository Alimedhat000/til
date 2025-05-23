# Promise-based HTTP Request Implementation

## Core Request Function

The main function that wraps XMLHttpRequest in a Promise:
  

```javascript

function get(url) {
  // Return a Promise
  // The Promise constructor gets a function called the executor
  // with two params:
  //   - resolve : function to call when success
  //   - reject : function to call when fail
  return new Promise(function (resolve, reject) {
    // Creates a new XMLHttpRequest object and inits the GET method
    const req = new XMLHttpRequest();
    req.open("GET", url);

    // Runs when the request completes loading
    req.onload = function () {

      // if HTTP OK(200) calls resolve else reject
      if (req.status == 200) {
        resolve(req.response);
      } else {
        reject(Error(req.statusText));
      }
    };
    // Runs if there's a network error during the request
    // calls reject with a network error
    req.onerror = function () {
      reject(Error("Network Error"));
    };
    // Actually sends the request to the server
    req.send();
  });
}

```
## Basic Usage
The get function can be used like this to make HTTP requests:
```javascript
get("story.json")
  .then(function (response) {
    // Runs on successful response
    console.log("Success!", response);
  })
  .catch(function (error) {
    // Runs if there's an error
    console.log("Failed!", error);
  })
  .finally(function () {
    // Always runs after then/catch
    console.log("finally this happens!");
  });
  ```
## Working with Multiple Promises
### Promise.all()
Takes an array of promises and waits for all to complete:
```javascript

Promise.all([promise1, promise2])
  .then(function (res) {
    // handle res after both promises are resolved
    // => res will be an array of results <=
  })
  .catch(function (error) {
    // handle one or more rejects
    // runs if any promise fails
  });
```
### Promise.race()
Returns the result of whichever promise settles first:
```javascript

Promise.race([req1, req2])
  .then(function (one) {
    // Gets result of the first promise to complete
    console.log("Then: ", one);
  })
  .catch(function (one, two) {
    // Gets first error if any promise fails
    console.log("Catch: ", one);
  });
```
## Key Concepts
- `Promise` constructor takes an executor function with `resolve` and `reject` parameters
- `XMLHttpRequest` handles the actual HTTP communication
- `then()` handles successful responses
- `catch()` handles errors
- `finally()` executes regardless of outcome
- `Promise.all()` waits for all promises to complete
- `Promise.race()` responds to the first settled promise