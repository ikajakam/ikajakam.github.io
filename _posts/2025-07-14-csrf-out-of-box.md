---
title: "CSRF Testing Out-of-the-Box"
date: 2025-07-14 00:00:00 +0530
categories: [Vulnerability, CSRF]
tags: [Pentesting, Bug Bounty, CSRF, Vulnerability]
---

![Alt Text](/img/csrf-out.png)

### A. CSRF via CORS Misconfig + Token Leakage

If any `api.example.com` endpoint has :

-   `Access-Control-Allow-Origin: *` (or reflects attacker origin)
-   And cookies are still sent (`Access-Control-Allow-Credentials: true`)
-   But the CSRF token is stored in a cookie or JS-accessible

Then combine **CORS + CSRF**
```js
fetch("https://api.digitalocean.com/account/delete",  
{
method:  	  "POST",  
credentials:  "include"  
});
```
- Test all subdomains and endpoints for CORS misconfigs!

###  B. **Clickjacking + CSRF Hybrid**

CSRF protections can be bypassed if the UI can be **framed**, and a user can be tricked into clicking a malicious overlay.

Use **X-Frame-Options Bypass** or **CSS clickjacking** with transparent overlays + auto-click or social engineering traps.

Test if `X-Frame-Options` or `Content-Security-Policy: frame-ancestors` is missing.

### C. **JSON CSRF / Content-Type Bypass**

Many APIs reject `application/x-www-form-urlencoded` or `multipart/form-data` — but accept only `application/json`.

#### Bypass idea:
-   Find if you can **inject JSON using unconventional ways**, like :
```js
<script>
fetch('https://digitalocean.com/api/modify-email', {
  method: 'POST',
  body: JSON.stringify({email: 'attacker@evil.com'}),
  headers: {'Content-Type': 'application/json'},
  credentials: 'include'
});
</script>
```
- If SameSite isn’t enforced, this could still work — JSON CSRF is real.

### D. **Token in Cookie, but No SameSite**

If you see a CSRF token **stored in a cookie**, and the cookie doesn’t have `SameSite=Strict` or `Lax`, you can often **send the token yourself**:
```js
<iframe src="https://victim.digitalocean.com/endpoint?token=<static_token>" />
```
- Or inject the cookie first with document.cookie = ... (if subdomain has XSS/CORS).

### E. **CSRF via OAuth Misuse**

Check these:

-   **Connecting a GitHub account**, or **revoking integrations**
-   Account linking flows (OAuth) that return to the app with `code=...&state=...`

If **state is guessable** or absent → CSRF possible via OAuth login hijack or unauthorized account linking.

### F. **Referer / Origin Header Downgrade**

If backend checks:

-   `Origin` or `Referer` for CSRF protection
-   But also accepts **Referer: null** (e.g., sent by iframes or downloads)

Then a **null origin bypass** might be possible. Use this trick :
```js
<iframe sandbox srcdoc="
  <form action='https://digitalocean.com/account/change-email' method='POST'>
    <input name='email' value='attacker@example.com'>
    <input type='submit'>
  </form>
  <script>document.forms[0].submit();</script>
"></iframe>
```
- Modern browsers sometimes strip Referer in sandboxed iframes.

### G. **Race Condition + CSRF**

Send multiple **simultaneous requests** with different payloads:

`# Use turbo intruder or Burp's parallel request extension`

-   Bypass token validation by **racing the server**
-   Or override one CSRF token while the other is valid.

### H. **Browser Auto-Fill CSRF**

If a form field (e.g., email or address) auto-fills and the server accepts POST without CSRF token :
```js
<form action="https://digitalocean.com/update-profile" method="POST">
  <input name="email" autocomplete="email">
  <input type="submit">
</form>
<script>document.forms[0].submit();</script>
```
- This abuses the auto-filled fields by the browser — no interaction needed.

### I. **Testing via Iframes + Minimal Interaction**

Build your CSRF PoC to **mimic legit UI**. Then:

-   Load it inside an iframe
-   Trick user into interaction using `pointer-events: none` overlays, fake CAPTCHA, etc.

##  Stealth Testing Tips
### Monitor CSRF tokens:

-   Is it **tied to session** or just static?
-   Does it **rotate per request or per session**?
-   Try reusing **old tokens**, see if they're still valid
-   Try **missing token, altered token, and empty token**

### Token in JavaScript?

-   Grep for `csrfToken` in JS responses
-   Test if you can read it via XSS/CORS or any reflected parameter

###  Tools / Extensions for CSRF Hunting

-   🧬 **Burp Turbo Intruder** (for race + bypass testing)
-   🛠 **CSRFScanner plugin** (automates PoC generation)
-   🧪 **Autorize** (for IDOR/CSRF combo testing)
-   🔬 **CORSy** (for automated CORS testing)
-   ⚔️ **Corsy.py + SameSiteTest** (check cookie SameSite status programmatically)

### Bonus — Custom Burp Macro to Strip CSRF Token

1.  Create a **Burp Repeater request**.
2.  Remove the `csrf_token` header or form field.
3.  Observe if the request still works.
4.  Use **Burp macros + session handling rules** to automate this during active scans.

### Target Zones
|Area|Endpoint Guess  |
|--|--|
|Account Deletion  |/billing/update  |
|SSH Key Add|/ssh/add
|2FA Disable|/security/2fa/disable
|API Token Revoke|/api/tokens/revoke
|Support Ticket Creation|/support/ticket/new
- Always check for legacy versions : e.g. `/v1/` , `/old/`, `/admin/` , `/mobile-api/`

### Next Move

1.  Focus on **legacy endpoints or admin-only APIs**
2.  Look for **CORS misconfig, null referer, SameSite misconfig**
3.  Test **OAuth and 3rd party integration CSRF**
4.  Abuse **sandboxed iframes + auto-submit**
5.  Combine **CSRF + Clickjacking + JSON bypasses**


