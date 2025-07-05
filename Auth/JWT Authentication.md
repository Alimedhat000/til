
JWT stands for (JSON Web Token) and is mainly used for authorization which is a bit different than authentication, where authentication is like taking a user and password and making sure they're correct, but authorization is making sure that the user sending a request is the same logged in user i.e. authorizing that this user has access to this particular system/request.

### **How JWT Works?**

JWTs are typically used in stateless authentication systems. When a user logs in with their credentials (authentication), the server validates them and generates a JWT with a secret key. This token is then sent back to the client, usually stored in localStorage or a cookie.

On every subsequent request to a protected route or resource, the client sends this token (typically in the `Authorization` header as a Bearer token). The server then verifies the token using a secret key or public/private key pair, and if it's valid, processes the request.

### **How It Differs from Sessions and Cookies?**

The **main difference** between **JWT** and **session-based authentication** is that with JWT, **nothing is stored on the server** after the user logs in. The entire user context (claims) is stored in the token itself, which the client holds and sends with each request.

Here's a deeper breakdown:
#### 1. **Stateless vs Stateful**

- **JWT** is **stateless** – once issued, the server does not need to store anything.
- **Sessions** are **stateful** – the server needs to store session data for each logged-in user.

This makes JWT better suited for **distributed systems** and **microservices**, where storing sessions across multiple servers can be a challenge.
#### 2. **Where Data is Stored**

- **Session-based**: The user's data (like user ID, role, permissions) is stored on the **server**, and the client just holds a session ID.
- **JWT-based**: The user’s data is **encoded directly into the token**, and stored on the **client-side** (like in `localStorage`, `sessionStorage`, or cookies).
#### 3. **Token Transparency**

- With JWT, you can **decode the token** on the client side (it’s Base64-encoded JSON) and inspect the data (unless encrypted).
- With sessions, the client can’t access what the server has stored. The session ID is just a random reference key.

#### 4. **Token Expiration and Revocation**

- **Sessions** can be manually revoked by deleting or invalidating the session ID on the server.
- **JWTs** are hard to revoke since the server doesn’t keep track of issued tokens. The usual approach is to:
    - Set **short expiration times** (`exp` claim)
    - Use **refresh tokens**
    - Implement a **blacklist** of tokens (optional, for higher security)

#### 5. **Cross-Origin Requests and Flexibility**

- **JWT** works better for APIs and Single Page Applications (SPAs), especially when frontend and backend are hosted on different domains.
- **Sessions** are simpler for traditional, server-rendered applications under the same domain.

![[Pasted image 20250424111938.png]]

### **JWT Structure**

A JWT consists of **three parts**, separated by dots (`.`):

1. **Header** – contains the type of token (JWT) and the signing algorithm (e.g., HS256, RS256).
2. **Payload** – contains the claims, i.e., the data we want
3. to transmit (user ID, email, roles, password etc.).
4. **Signature** – created using the header and payload along with the secret key allows us to know that the token hasn't been changed by the client.
   
![[Pasted image 20250702175145.png|center]]


### **JWT in NodeJS and Express**:

There’s no one right way to do JWT auth in express but using `passport-jwt` as the middleware with `jsonwebtoken` NPM module 