Today I learned how to **securely hash passwords** using `crypto.pbkdf2Sync`  —Which means *Password-Based Key Derivation Function 2 — Synchronous*—  and the role of a **salt** in that process. 

### 🧂 What is a Salt?

A **salt** is a random string added to a password **before hashing**. Its purpose is to:
- Prevent **rainbow table** attacks (precomputed hash lookups)
- Ensure that **identical passwords produce different hashes**
- Make brute-force attacks more computationally expensive

### Workflow 

```js 
function genPassword(password) {
  const salt = crypto.randomBytes(32).toString("hex");//256-bit random salt
  const genHash = crypto
    .pbkdf2Sync(password, salt, 10000, 64, "sha512")// Key derivation
    .toString("hex");

  return {
    salt: salt,
    hash: genHash,
  };
}

```

💡 `pbkdf2Sync` is a key derivation function (KDF) that:

- Takes a password and salt
- Rehashes the output of the previous hash **10,000** times.
- Produces a 64-byte (512-bit) hash using SHA-512 —a strong hashing algorithm—
  
  