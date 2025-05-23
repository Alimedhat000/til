## What Are User Authentication Choices?

When building secure applications, authenticating users is a key step. There are several common methods used to manage authentication. Below are three popular choices:

1. Session-Based Authentication
How it works: When a user logs in, the server creates a session and stores it (typically in memory or a database). A session ID is then sent to the user's browser via a cookie.

On each request: The cookie is sent with every HTTP request, allowing the server to verify the session ID and identify the user.

- Pros:
	- Secure (especially when used with HTTPS and HTTP-only cookies)
	- Server can easily invalidate sessions

- Cons:
	- Requires server-side storage
	- Not ideal for scalable or distributed systems without extra setup (e.g., shared session store like Redis)

2. JWT (JSON Web Token) Authentication
How it works: After a successful login, the server generates a signed JWT containing user data and sends it to the client. The client stores this token (usually in localStorage or sessionStorage).

On each request: The token is sent in the Authorization header.

- Pros:
	- Stateless and scalable — no server-side session storage needed
	- Can contain user roles and claims inside the token

- Cons:
	- Cannot easily revoke tokens unless you implement a token blacklist
	- Larger token size due to payload

3. OAuth (Open Authorization)
How it works: Used for granting third-party applications limited access to user data without exposing credentials. Often used with "Login with Google/Facebook/etc."

Flow: Involves redirecting the user to an authorization server, granting permissions, then receiving an access token.

- Pros:
	- Widely adopted and secure when implemented correctly
	- Great for integrating with third-party APIs

- Cons:
	- More complex to implement
	- Requires handling token expiration and refresh logic


---
## What is Passport JS?

**Passport.js** is a popular **authentication middleware for Node.js**. It provides a flexible and modular approach to authenticate requests in your Node.js applications.

### What Is It?

- Passport is **middleware** used in Express or similar frameworks.
- It doesn’t handle sessions, cookies, or user management directly — instead, it gives you **tools to plug into these systems**.
- Think of it like a plug-and-play system where you choose what *strategy* you want to authenticate users with.
###  Passport Strategies ??

A **strategy** in Passport.js defines **how a user is authenticated**. Passport supports hundreds of strategies to authenticate users in different ways — from local login forms to third-party services like Google, Facebook, and GitHub.

|Strategy Type|Example Strategies|Description|
|---|---|---|
|Local|`passport-local`|Username/password login|
|Token-based|`passport-jwt`|JWT bearer token auth for APIs|
|OAuth2|`passport-google-oauth20`, etc.|Third-party social logins|
|SSO/Enterprise|`passport-saml`, `passport-ldap`|Enterprise-level authentication|


---
## HTTP Headers and Cookies  

When implementing user authentication, understanding HTTP headers and cookies is essential. These components enable the secure exchange of data between the client and server, particularly in session-based and token-based authentication systems.

### HTTP Headers

HTTP headers are metadata included in both HTTP requests and responses. They allow the client and server to share additional information beyond the core request and response payloads. The following headers are commonly used in authentication:

- **Authorization**  
    Used to transmit credentials or tokens in request headers.
    
    `Authorization: Bearer <JWT>`

	Commonly utilized in token-based authentication, particularly with JWT.
    
- **Set-Cookie**  
    Sent by the server to instruct the client to store a cookie.

    `Set-Cookie: sessionId=abc123; HttpOnly; Secure; SameSite=Strict`
    
- **Cookie**  
    Sent automatically by the client with each request after a cookie has been set.
    `Cookie: sessionId=abc123`
    

---

### Cookies

Cookies are small data fragments stored in the user's browser. In authentication workflows, they are often used to persist session identifiers or tokens.

#### Key Attributes:

- **HttpOnly**: Restricts JavaScript access to the cookie, mitigating XSS attacks.

- **Secure**: Ensures cookies are only transmitted over HTTPS.

- **SameSite**: Controls whether cookies are sent with cross-site requests. Options include:
    - `Strict`: Cookie is not sent with cross-site requests.
    - `Lax`: Allows some cross-site requests (e.g., top-level navigation).
    - `None`: Cookies are sent with all requests (requires `Secure` flag).


---
## Express Session Library