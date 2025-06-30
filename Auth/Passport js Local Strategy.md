The `LocalStrategy` is used to authenticate users by comparing a username and password with values stored in your database.

**`VerifyCallBack`**
```js
async function verifyCallback(username, password, done) {
  User.findUserByUsername(username)
    .then((user) => {
      if (!user) {
        return done(null, false, { message: "Incorrect username." });
      }

      if (!verifyPassword(password, user.hash, user.salt)) {
        return done(null, false, { message: "Incorrect password." });
      }

      return done(null, user);
    })
    .catch((err) => {
      return done(err);
    });
}
```

> `done(error, user, options)` is the signature. If no error and authentication fails, `user` should be `false`.

**`VerifyPassword` Helper**

```python 
function verifyPassword(password, hash, salt) {
  const hashVerify = crypto
    .pbkdf2Sync(password, salt, 10000, 64, "sha512")
    .toString("hex");
  return hash === hashVerify;
}
```

Then Register the Strategy

```js
const localStrategy = new LocalStrategy(verifyCallback);
passport.use(localStrategy);
```

Optionally,  you can customize the field names:

```js 
const customFields = {
  usernameField: "uname",
  passwordField: "pw",
};

const localStrategy = new LocalStrategy(customFields, verifyCallback);
passport.use(localStrategy);

```
