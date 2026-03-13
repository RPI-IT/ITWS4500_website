# React Frontend for JWT Authentication

This guide walks through building a React frontend for the JWT authentication API and explains the critical security decisions around where tokens are stored in the browser.

## Setup

**1. Add static file serving to `server.js`** (after `app.use(express.json())`):

```js
const path = require('path');
app.use(express.static(path.join(__dirname, 'public')));
```

**2. Create the frontend file:**

```bash
mkdir -p public
touch public/index.html
```

**3. No build step needed.** We use React via CDN in a single HTML file:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>JWT Auth Demo</title>
</head>
<body>
  <div id="root"></div>
  <script src="https://unpkg.com/react@18/umd/react.development.js"></script>
  <script src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
  <script type="text/babel">
    // Your React code goes here
  </script>
</body>
</html>
```

Visit `http://localhost:3000` after starting the server — Express serves `public/index.html` automatically at the root path.

---

## The React App

The frontend has three sections: Register, Login, and a protected "Get Secret" button.

### State Management

```js
const { useState } = React;

function App() {
  // Auth state
  const [token, setToken] = useState(null);
  const [username, setUsername] = useState('');

  // Form inputs
  const [regUser, setRegUser] = useState('');
  const [regPass, setRegPass] = useState('');
  const [loginUser, setLoginUser] = useState('');
  const [loginPass, setLoginPass] = useState('');

  // Response messages
  const [regMsg, setRegMsg] = useState(null);
  const [loginMsg, setLoginMsg] = useState(null);
  const [secretMsg, setSecretMsg] = useState(null);
```

### Calling the API with `fetch()`

**Register** — `POST /api/register`:

```js
async function handleRegister(e) {
  e.preventDefault();
  const res = await fetch('/api/register', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ username: regUser, password: regPass })
  });
  const data = await res.json();
  // data.message on success, data.error on failure
}
```

**Login** — `POST /api/login`, then store the token in React state:

```js
async function handleLogin(e) {
  e.preventDefault();
  const res = await fetch('/api/login', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ username: loginUser, password: loginPass })
  });
  const data = await res.json();
  if (res.ok) {
    setToken(data.token);        // store access token
    setUsername(loginUser);
  }
}
```

**Access protected route** — `GET /api/secret` with the `Authorization` header:

```js
async function handleGetSecret() {
  const res = await fetch('/api/secret', {
    headers: { 'Authorization': `Bearer ${token}` }
  });
  const data = await res.json();
  // data.message on success, data.error on 401/403
}
```

**Logout** — clear the token from state:

```js
function handleLogout() {
  setToken(null);
  setUsername('');
}
```

---

## Where Should Tokens Be Stored? (Security Deep Dive)

This is the most important security decision in a frontend auth system. Our demo stores the access token in React state (`useState`). Here's why that matters, and how each storage option compares.

### The Two Tokens

Our API returns two tokens on login:

| | Access Token | Refresh Token |
|---|---|---|
| **Purpose** | Authorize API requests | Get new access tokens |
| **Lifetime** | Short (15 minutes) | Long (7 days) |
| **Sent how** | `Authorization: Bearer <token>` header | `POST /api/refresh` body |
| **If stolen** | Attacker has access for minutes | Attacker has access for days |
| **Risk level** | Lower (short window) | Higher (long window) |

Because these tokens have very different risk profiles, **they should be stored differently**.

### Storage Options Compared

#### 1. React State (in-memory variable)

```js
const [token, setToken] = useState(null);
```

- **Accessible to XSS?** Technically yes (malicious JS could read React internals), but there's no persistent copy to steal.
- **Survives page refresh?** No — user must log in again.
- **Survives tab close?** No.
- **Best for:** Access tokens. The short lifetime + no persistence means even if an attacker reads it, the window is tiny and it's gone on refresh.

#### 2. `localStorage`

```js
localStorage.setItem('token', data.token);
const token = localStorage.getItem('token');
```

- **Accessible to XSS?** Yes — any JavaScript on the page can read it.
- **Survives page refresh?** Yes.
- **Survives tab close?** Yes — persists until explicitly removed.
- **Risk:** If an attacker injects a script (XSS), they can silently exfiltrate the token to their own server. The token persists even after the user closes the browser.
- **Best for:** Low-sensitivity data. **Not recommended for tokens.**

#### 3. `sessionStorage`

```js
sessionStorage.setItem('token', data.token);
const token = sessionStorage.getItem('token');
```

- **Accessible to XSS?** Yes — same as `localStorage`.
- **Survives page refresh?** Yes.
- **Survives tab close?** No — cleared when the tab closes.
- **Risk:** Still vulnerable to XSS, but the exposure window is limited to the browser session.
- **Best for:** Access tokens if you need refresh survival but accept XSS risk.

#### 4. HTTP-Only Cookie (set by the server)

```js
// Server-side (Express):
res.cookie('refreshToken', refreshToken, {
  httpOnly: true,    // JavaScript CANNOT read this cookie
  secure: true,      // Only sent over HTTPS
  sameSite: 'strict', // Not sent on cross-site requests
  maxAge: 7 * 24 * 60 * 60 * 1000  // 7 days
});

// Client-side:
// Nothing to do — the browser sends the cookie automatically
```

- **Accessible to XSS?** No — `httpOnly` prevents all JavaScript access.
- **Survives page refresh?** Yes.
- **Survives tab close?** Yes — until `maxAge` expires.
- **Risk:** Vulnerable to CSRF (cross-site request forgery) unless you use `sameSite` and/or CSRF tokens.
- **Best for:** Refresh tokens. Long-lived + maximum protection from XSS.

### The Recommended Pattern

```
Access Token  → React state (in-memory)
Refresh Token → HTTP-only secure cookie (set by server)
```

Here's why this combination works:

1. **Access token in memory** — lives only as long as the page is open, expires in 15 minutes, and is sent explicitly in the `Authorization` header (not automatically like cookies, so no CSRF risk).

2. **Refresh token in HTTP-only cookie** — JavaScript can never read it, so XSS can't steal it. When the access token expires, the frontend calls `POST /api/refresh` and the browser automatically includes the cookie.

3. **On page refresh** — the access token is gone (memory cleared), but the refresh token cookie is still there. The app calls `/api/refresh` on load to silently get a new access token.

### Attack Scenarios

| Attack | localStorage | Memory + HTTP-only cookie |
|---|---|---|
| **XSS** (attacker injects script) | Attacker steals both tokens, has access for days | Attacker can make requests during the session but can't exfiltrate the refresh token |
| **CSRF** (attacker triggers request from another site) | Not affected (tokens in headers) | Blocked by `sameSite: 'strict'` on the cookie |
| **Tab left open** | Tokens sitting in storage indefinitely | Access token expires in 15 min, refresh token is invisible to scripts |
| **Network sniffing** | Tokens visible if not HTTPS | `secure: true` on cookie ensures HTTPS only |

### What Our Demo Does (and Why It's OK for a Lab)

Our demo stores the access token in React state — the simplest secure-enough approach:

```js
const [token, setToken] = useState(null);  // access token in memory
```

It does **not** use HTTP-only cookies for the refresh token because that requires server-side cookie handling (`res.cookie()`, `cookie-parser` middleware), which adds complexity beyond the scope of this lab.

For a production app, you'd upgrade to the full pattern: access token in memory + refresh token in an HTTP-only cookie + a silent `/api/refresh` call on page load.

---

## Complete Solution

See `public/index.html` in this repository for the working React frontend.
