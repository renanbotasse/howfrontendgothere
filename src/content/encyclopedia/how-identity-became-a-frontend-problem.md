
In the server-rendered web, authentication was simple enough to not think hard about. A user submits a form with their username and password. The server verifies them. If correct, the server sets a cookie with a session ID. On subsequent requests, the browser sends that cookie automatically. The server reads the session ID, looks up who it belongs to, and renders the page for that user.

The browser's role in this is purely mechanical. It stores the cookie, sends the cookie, follows redirects. There's no JavaScript involved in the authentication flow. The frontend doesn't manage identity.

The SPA era changed this.

---

## Why SPAs needed a different approach

A SPA runs in the browser and communicates with a backend API. When the user logs in, the API needs to return something the SPA can use to authenticate future API calls. Cookies still work for this, but they have constraints when the frontend and backend are on different domains, the same-origin policy and CORS complicate cookie handling for cross-origin requests.

The SPA era adopted tokens: the server returns a JWT (JSON Web Token) on successful login, the client stores it, and attaches it as a header on every subsequent API call.

```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

This is explicit, controllable, and works across domains. It also introduced a category of security problem that didn't exist with server-managed sessions: where do you store the token?

---

## The token storage problem

A JWT stored in `localStorage` persists across browser sessions and tabs. It's easy to read and write. But it's also accessible to any JavaScript running on the page. If your application has an XSS vulnerability, even a minor one, and an attacker injects a script, that script can read `localStorage` and steal the token. A stolen JWT gives the attacker authenticated access to your API until the token expires.

A JWT stored in an `httpOnly` cookie is not accessible to JavaScript. XSS can't steal it. But cookies are only sent automatically to same-domain requests. Cross-origin requests need `credentials: 'include'` on the fetch call and `Access-Control-Allow-Credentials: true` on the server, and CORS policy doesn't allow wildcard origins when credentials are included, so you have to explicitly list allowed origins.

A JWT stored in memory (JavaScript variable, React state) is the most secure, no storage means nothing to steal. But it's gone on page refresh. The user has to log in again whenever they open a new tab or refresh the page.

There's no perfect answer. The standard recommendation became: use `httpOnly` cookies for the refresh token, use in-memory state for the access token. The access token is short-lived (15 minutes), the refresh token is long-lived but can't be read by JavaScript. When the access token expires, request a new one using the refresh token.

---

## OAuth and the identity providers

OAuth 2.0 lets users authenticate with a third-party identity provider, "Sign in with Google," "Sign in with GitHub", without sharing their password with your application. Your application redirects to the provider, the provider authenticates the user, and redirects back with a code that your server can exchange for an access token.

OIDC (OpenID Connect) extends OAuth 2.0 to include identity information. The provider returns an ID token, a JWT containing claims about the user's identity, in addition to the access token.

From a frontend perspective, this means navigating to the provider, handling the redirect back, and exchanging the code. Libraries like Auth.js (formerly NextAuth) and Clerk abstract most of this. The implementation details, PKCE flow, state parameter, nonce, exist to prevent attacks like authorization code interception, and handling them correctly in a custom implementation requires care.

---

## Auth-as-a-Service

Authentication is one of those things that's easy to get subtly wrong and the consequences of getting it wrong are severe. Password hashing (bcrypt, argon2), rate limiting login attempts, secure session invalidation, email verification, password reset flows, multi-factor authentication, implementing all of this correctly, and keeping it current as security best practices evolve, is work that doesn't differentiate most applications.

Auth0, Okta, and Clerk provide authentication as a service. You integrate their SDK, configure your providers and rules, and let them manage the security infrastructure. The tradeoff is cost, dependency, and some loss of control over the user experience.

For most applications, the tradeoff is worth it. The security expertise embedded in a dedicated auth service is hard to replicate, and the surface area for mistakes in a custom implementation is large.

---

## JWTs, the misunderstood token

JWTs contain claims encoded as JSON, signed with a key the server controls. The signature lets the server verify that a token it receives was issued by itself and hasn't been tampered with. Because the token is self-contained, the server doesn't need to look up a session in a database, it reads the token and trusts what's in it.

The self-contained nature is both the advantage and the limitation. Because there's no server-side session to invalidate, a JWT remains valid until it expires even after the user "logs out." Logout in a pure JWT system means deleting the token on the client, but if an attacker already has a copy of the token, it's still valid on the server.

Short expiration times and refresh token rotation mitigate this, but it means JWT-based authentication has slightly weaker revocation semantics than session-based authentication. For applications where being able to forcibly invalidate a user's session is important, e.g., detecting account compromise and immediately logging out all sessions, server-side session management or a token blacklist (which re-introduces the database lookup) is needed.

---

## The frontend's security responsibility

The SPA era put authentication logic in the browser, and with it, the responsibility for handling it correctly. XSS, CSRF, token storage, redirect handling after login, these are frontend concerns that have direct security implications.

The trend toward server-rendered applications with server-side auth handling (as in Next.js App Router with Auth.js, or Remix's cookie-based sessions) is partly a response to this. When authentication runs on the server, the browser's role shrinks back toward what it was before SPAs. The security surface area on the client is smaller, and the logic that's hardest to get right moves back to where it's easier to secure.

---

*Next: How We Learned to Measure Performance. LCP, FID, CLS, INP, the way the industry talks about web performance has become more precise. Understanding what each metric measures and why it matters changes how you approach performance work.*
