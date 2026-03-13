# Upgrading from bcrypt to Argon2id

Argon2id is the winner of the 2015 Password Hashing Competition and is recommended by OWASP for password storage. It's more resistant to GPU and ASIC-based brute-force attacks than bcrypt because it's memory-hard — it requires a large amount of RAM to compute, not just CPU time.

## 1. Install argon2

```bash
npm install argon2
```

> **Note:** Unlike `bcryptjs`, the `argon2` package requires native compilation. This works fine in Codespaces but may need build tools (Python, make, g++) on other systems.

## 2. Update `server.js`

**Replace the require:**

```js
// Before
const bcrypt = require('bcryptjs');

// After
const argon2 = require('argon2');
```

**Replace the hash in the registration route:**

```js
// Before
const hashedPassword = await bcrypt.hash(password, 10);

// After
const hashedPassword = await argon2.hash(password, { type: argon2.argon2id });
```

**Replace the compare in the login route:**

```js
// Before
const validPassword = await bcrypt.compare(password, user.password);

// After
const validPassword = await argon2.verify(user.password, password);
```

That's it — three lines changed. The API behavior and all 8 autograder tests remain identical.

## Why Argon2id over bcrypt?

| | bcrypt | Argon2id |
|---|---|---|
| **Attack resistance** | CPU-hard only | CPU-hard + memory-hard |
| **GPU cracking** | Partially resistant | Highly resistant |
| **Tunability** | Cost factor only | Time, memory, and parallelism |
| **Max password length** | 72 bytes | No practical limit |
| **Recommended by** | Still acceptable | OWASP, NIST (2024+) |
