## What is Passport JS?

**Passport.js** is a popular **authentication middleware for Node.js**. It provides a flexible and modular approach to authenticate requests in your Node.js applications.

### What Is It?

- Passport is **middleware** used in Express or similar frameworks.
- It doesn’t handle sessions, cookies, or user management directly — instead, it gives you **tools to plug into these systems**.
- Think of it like a plug-and-play system where you choose what *strategy* you want to authenticate users with.

###  Passport Strategies ??

A **strategy** in Passport.js defines **how a user is authenticated**. Passport supports hundreds of strategies to authenticate users in different ways — from local login forms to third-party services like Google, Facebook, and GitHub.

| Strategy Type  | Example Strategies               | Description                     |
| -------------- | -------------------------------- | ------------------------------- |
| Local          | `passport-local`                 | Username/password login         |
| Token-based    | `passport-jwt`                   | JWT bearer token auth for APIs  |
| OAuth2         | `passport-google-oauth20`, etc.  | Third-party social logins       |
| SSO/Enterprise | `passport-saml`, `passport-ldap` | Enterprise-level authentication |

### Middleware Flow
This is where you actually apply auth to your user’s experience. The middleware has three parts: setup, the login flow, and “the see if they’re logged in or not” bit.

#### 1. Setup

In your main app file:

```js
app.use(passport.initialize())
app.use(passport.session())
```

- `passport.initialize()` creates the passport object on the request and defines where the shorthand user is located on the `req` object (defaults to `req.user`).

- `passport.session()` sets up calls the serialize && deserialize methods (we’ll talk about those later) and interacts with express-session. You have to have `app.use`d express-session before this or it will freak out.
#### 2.Login flow

Here you add a “begin login for this flow” URL. If you’re using multiple strategies, you’ll want a separate login URL for each. The `passport.authenticate(‘something’)` is used here, where `something` is the internal name of the strategy.

```js title:LocalStrategy
app.post('/login', passport.authenticate('local', {
  successRedirect: '/dashboard',
  failureRedirect: '/login',
}));
```

Many strategies also have a second endpoint for handling the callback from that authentication flow. Take the `FacebookStrategy`: this `returnUrl` handler takes the special parameters that come back in the URL and make calls to the Facebook API (using API Key configuration you defined in the Strategy config) and get back the `profile`. This `profile` is what you get back in the Strategy handler (I said remember it).
```js title:OAuth
// Step 1: Redirect to Google
app.get('/auth/google', passport.authenticate('google', { scope: ['profile'] }));

// Step 2: Handle callback
app.get('/auth/google/callback',
  passport.authenticate('google', { failureRedirect: '/login' }),
  (req, res) => {
    res.redirect('/dashboard');
  }
);

```

#### 3. Authentication Check Middleware

This is middleware you put in yourself. At any endpoint, you can add a check that ensures they’re logged in or not, by a check to `req.isAuthenticated()`. If the endpoint is protected, don’t forget to make sure it’s protected with this middleware. Otherwise, anyone will be allowed to use that endpoint.

```js
app.get("/dashboard",(req,res)=>{
	if (req.isAuthenticated){
		//Continue to Page
	}
	else{
		res.redirect('/login')
	}
})
```

### Session

So remember when during the strategy setup, you created (or found) the user and returned your “local representation of that user”? Well you need to do that again, based on the session information, since they’re not logging in every time.

#### deserializeUser

This method is called during the `passport.session()` step. It accepts the session token (what comes out of serializeUser) and returns either the “local representation of the user” or `false` (not logged in). This is called on every request, logged in or not.

```js
passport.deserializeUser((id, done) => {
  User.findById(id).then(user => {
    done(null, user); // attach user object to req.user
  }).catch(err => done(err));
});
```

#### serializeUser

This method is called RIGHT AFTER the Strategy handler. It accepts the “local representation of the user” and it should return a `string` (usually). That string is stored in the session as the identifier for the user, and it is what is passed into `deserializeUser` the next time this user makes a request. The output `string` should be “strategy agnostic”. The string ALONE should be enough to tell you how to look this user up in your database.

```js
passport.serializeUser((user, done) => {
  done(null, user.id); // store user ID in session
});
```


#### Logout 
In Passport v0.6.0+, you **must provide a callback** to `req.logout()`:
```js
app.get('/logout', (req, res, next) => {
  req.logout(function(err) {
    if (err) return next(err);
    res.redirect('/');
  });
});

```
