**[Supertest](https://www.npmjs.com/package/supertest)** is a high-level HTTP testing library for Node.js, designed to simplify the process of testing HTTP servers and APIs. Built on top of the `superagent` HTTP client, it provides a fluent and expressive API for making requests and asserting responses in tests.

**Supertest** works with any test framework, here is an example without using any test framework at all:

```ts
const request = require('supertest');
const express = require('express');

const app = express();

app.get('/user', function(req, res) {
  res.status(200).json({ name: 'john' });
});

request(app)
  .get('/user')
  .expect('Content-Type', /json/)
  .expect('Content-Length', '15')
  .expect(200)
  .end(function(err, res) {
    if (err) throw err;
  });
```