## What Are User Authentication Choices?

When building secure applications, authenticating users is a key step. There are several common methods used to manage authentication. Below are three popular choices:

1. **Session-Based Authentication** :
   
- **How it works:** When a user logs in, the server creates a session and stores it (typically in memory or a database). A session ID is then sent to the user's browser via a *cookie*.

- **On each request:** The cookie is sent with every HTTP request, allowing the server to verify the session ID and identify the user.

- **Pros**:
	- Secure (especially when used with HTTPS and HTTP-only cookies)
	- Server can easily invalidate sessions

- **Cons**:
	- Requires server-side storage
	- Not ideal for scalable or distributed systems without extra setup (e.g., shared session store like Redis)
	  
![[Pasted image 20250628190436.png|center]]


2. **JWT (JSON Web Token) Authentication** :
   
- **How it works:** After a successful login, the server generates a **signed JWT** containing user data and sends it to the client. The client stores this token (usually in localStorage or sessionStorage).

- **On each request:** The client includes the JWT in the request headers. The server validates the signature and uses the token's data for authentication.

- **Pros:**
	- Stateless and scalable — No server-side session storage needed
	- Can contain user roles and claims inside the token
	- JWTs can be used across microservices, allowing services to verify the token without contacting the authentication server.

- **Cons:**
	- A stolen JWT can be used until it expires. To mitigate this, short-lived access tokens are often combined with long-lived refresh tokens.
	- Larger token size due to payload.

![[Pasted image 20250628190636.png|center]]

2. **OAuth (Open Authorization)**
   
- **How it works:** Used for granting third-party applications limited access to user data without exposing credentials. Often used with "Login with Google/Facebook/etc."

- **Flow:** Involves redirecting the user to an authorization server, granting permissions, then receiving an access token.

- Pros:
	- Widely adopted and secure when implemented correctly
	- Great for integrating with third-party APIs

- Cons:
	- More complex to implement
	- Requires handling token expiration and refresh logic


