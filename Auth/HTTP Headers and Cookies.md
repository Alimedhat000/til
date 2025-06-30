
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
    `Cookie: sessionId=abc123`.
    

---
### Cookies

Cookies are small data fragments stored in the user's browser. In authentication workflows, they are often used to persist session identifiers or tokens.

#### Key Attributes:

- **HttpOnly**: Restricts JavaScript access to the cookie, mitigating XSS attacks.

- **Secure**: Ensures cookies are only transmitted over HTTPS.

- **Expires**: Shows when this cookie is expired and is no longer sent with the request.

- **SameSite**: Controls whether cookies are sent with cross-site requests. Options include:
    - `Strict`: Cookie is not sent with cross-site requests.
    - `Lax`: Allows some cross-site requests (e.g., top-level navigation).
    - `None`: Cookies are sent with all requests (requires `Secure` flag).

