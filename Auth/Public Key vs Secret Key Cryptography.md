The Two Primary models in modern cryptography systems is:
- Secret Key Cryptography (Symmetric)
- Public Key Cryptography (Asymmetric)

### Secret Key Cryptography

Secret Key Cryptography, also called **symmetric encryption**, uses a **single key** for both encrypting and decrypting data. This means the sender and receiver **must both have access to the same secret key** and keep it confidential.

```text
Encrypt:   Plaintext + Secretkey -> Ciphertext
Decrypt:   Ciphertext + Secretkey -> Plainext
```

This system is ideal for encrypting **large amounts of data or streaming** because it’s **very fast**. However, securely sharing the secret key is **non-trivial**, and it does **not inherently verify** the sender's identity.

**Use Cases:**
- Encrypting files, databases, and backups.
- Secure communication **within trusted environments**.    
- VPN tunnels and TLS **after** handshake (bulk encryption).

### Public Key Cryptography

Public Key Cryptography uses a **pair of keys** — one public, one private. The public key is used to encrypt data, and the private key is used to decrypt it. These keys are **mathematically related** but **not interchangeable**.

```text
Encrypt:   Plaintext + PublicKey → Ciphertext  
Decrypt:   Ciphertext + PrivateKey → Plaintext
```
>The **public key** can be shared with anyone.  
>Only the **private key holder** can decrypt messages encrypted with the corresponding public key.

There’s **no need to share a secret**, since one key is public and the other is kept private. Public key systems can also **verify identity** through digital signatures. However, this system is **much slower** because it involves **complex mathematical operations** (e.g., prime factorization, elliptic curves).

**Use Cases:**
- Secure communication over untrusted channels (e.g., HTTPS).
- Digital signatures and software package validation.
- Secure key exchange (e.g., exchanging AES keys).
- Email encryption (e.g., PGP, S/MIME).

### Combined Usage: Hybrid Cryptography
 
Most secure systems (like HTTPS) use **both** types together:

1. **Key Generation (asymmetric):**  
    The server (or recipient) generates a public/private key pair.
    
2.  **Session Key Generation (symmetric):**  
    The client generates a random **symmetric key** (often called a _session key_).
    
3. **Encrypt the session key:**  
    The client encrypts the session key using the server’s **public key**.
    
4. **Send encrypted session key to server:**  
    Only the server can decrypt this key using its **private key**.
    
5. **Symmetric communication begins:**  
    Both client and server now have the same symmetric key and use it to encrypt/decrypt the actual data using a fast symmetric algorithm like AES.

> ✅ This approach gives us the **security of asymmetric** and the **speed of symmetric** encryption.
