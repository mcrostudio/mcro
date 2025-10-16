````markdown
# Redaction Guide

**Share JSON safely without breaking structure (≤ 5 minutes).**

---

## 1) Purpose

Enable anyone (including non-engineers) to remove secrets from workflow/export JSON **without** breaking the JSON or losing what’s needed for debugging.

---

## 2) Scope — what to redact vs. keep

### Must redact (always)
- API keys, bearer/refresh tokens, session cookies  
- Passwords, OAuth `client_secret`, Basic auth credentials  
- Webhook/signing/HMAC secrets  
- Credentials embedded in URLs/connection strings (user, password, secret query params)

### May redact (policy/context)
- PII (emails, phone numbers, addresses, names)  
- Internal endpoints or internal IDs if sensitive  
- Proprietary field names (rare; keep structure when possible)

### Keep (usually not secret; often vital for debugging)
- Non-secret IDs (e.g., order IDs), timestamps, enums, error codes, step names

### Core principles
- **Preserve shape** (keys, arrays, nesting)  
- **Replace values only** with standard placeholders  
- **Keep JSON valid** (lint before sharing)

---

## 3) Standard placeholders

Use **ALL-CAPS angle-bracket** tokens so changes are obvious.

| Placeholder | Use for |
|---|---|
| `<REDACTED_TOKEN>` | Bearer/OAuth/Session tokens |
| `<REDACTED_API_KEY>` | Generic API keys |
| `<REDACTED_GOOGLE_API_KEY>` | Google API keys (AIza…) |
| `<REDACTED_BASIC_CREDENTIALS>` | Basic auth header credentials |
| `<REDACTED_CLIENT_SECRET>` | OAuth client secret |
| `<REDACTED_WEBHOOK_SECRET>` | Webhook/signing/HMAC secrets |
| `<REDACTED_USER>` / `<REDACTED_PASSWORD>` | URL/connection-string creds |
| `<REDACTED_AWS_ACCESS_KEY_ID>` | AWS Access Key ID (AKIA…/ASIA…) |
| `<REDACTED_AWS_SECRET>` | AWS Secret Access Key |
| `<REDACTED>` | Catch-all for secret query values |

---

## 4) Five-minute flow

1. **Duplicate** the file (work on the copy).  
2. **Regex find/replace** using the patterns below (A→D).  
3. **Manual sweep** for: `apiKey`, `token`, `secret`, `password`, `clientSecret`.  
4. **Validate JSON** (e.g., `jq . < file.json` or any linter).  
5. **Spot-check:** secrets gone, structure intact, placeholders present.  

> **Backreferences:** your editor may use `$1/$2` or `\1/\2`. If the replacement looks wrong, swap notation.

---

## 5) High-value regex (PCRE-style)

Enable case-insensitive where `(?i)` appears. Patterns aim for broad editor/engine support.

### A) Authorization headers
```regex
# Bearer
FIND:    ("Authorization"\s*:\s*")Bearer\s+[^"]+(")
REPLACE: $1Bearer <REDACTED_TOKEN>$2

# Basic
FIND:    ("Authorization"\s*:\s*")Basic\s+[A-Za-z0-9+/=]+(")
REPLACE: $1Basic <REDACTED_BASIC_CREDENTIALS>$2

# Token token=...
FIND:    ("Authorization"\s*:\s*")Token\s+token=[^"]+(")
REPLACE: $1Token token=<REDACTED_TOKEN>$2
````

### B) Key/secret fields

```regex
# apiKey-style fields (avoids bare "key" to reduce false positives)
FIND:    ("(?i)(api[_-]?key|x[-_]api[-_]key)"\s*:\s*")([^"]+)(")
REPLACE: $1<REDACTED_API_KEY>$3

# OAuth client secret
FIND:    ("(?i)client[_-]?secret"\s*:\s*")([^"]+)(")
REPLACE: $1<REDACTED_CLIENT_SECRET>$3

# Access/refresh tokens
FIND:    ("(?i)(?:access|refresh)[_-]?token"\s*:\s*")([^"]+)(")
REPLACE: $1<REDACTED_TOKEN>$3

# Webhook/signing/HMAC secrets
FIND:    ("(?i)(?:webhook|sign(?:ing)?|hmac)[_-]?(?:secret|key)"\s*:\s*")([^"]+)(")
REPLACE: $1<REDACTED_WEBHOOK_SECRET>$3
```

### C) Provider-specific

```regex
# Google API key
FIND:    AIza[0-9A-Za-z_\-]{35}
REPLACE: <REDACTED_GOOGLE_API_KEY>

# AWS Access Key ID
FIND:    (?:AKIA|ASIA)[0-9A-Z]{16}
REPLACE: <REDACTED_AWS_ACCESS_KEY_ID>

# AWS Secret Access Key (fielded)
FIND:    ("(?i)secret(access)?key"\s*:\s*")([^"]+)(")
REPLACE: $1<REDACTED_AWS_SECRET>$3
```

### D) URLs & connection strings

```regex
# URL credentials (broad; no lookbehind)
FIND:    ([a-z]+:\/\/)([^:@\/\s]+):([^@\/\s]+)@
REPLACE: $1<REDACTED_USER>:<REDACTED_PASSWORD>@

# DB/queue connection strings
FIND:    (postgres|mysql|mongodb(?:\+srv)?|redis|amqp):\/\/([^:@\/\s]+):([^@\/\s]+)@
REPLACE: $1://<REDACTED_USER>:<REDACTED_PASSWORD>@

# Secret query params (replace value only)
FIND:    ([?&](?:token|secret|sig|signature|key)=)[^&"\s]+
REPLACE: $1<REDACTED>
```

---

## 6) Before/After (inline, structure preserved)

**1) Header token**

**BEFORE**

```json
{ "request": { "headers": { "Authorization": "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.abc123" } } }
```

**AFTER**

```json
{ "request": { "headers": { "Authorization": "Bearer <REDACTED_TOKEN>" } } }
```

**2) URL with creds & query key**

**BEFORE**

```json
{ "step": { "url": "https://user123:superSecret!@api.example.com/v1?apikey=AIzaSyD3xYABCDEFGHIJKLMNOPQRSTUVWX&q=test" } }
```

**AFTER**

```json
{ "step": { "url": "https://<REDACTED_USER>:<REDACTED_PASSWORD>@api.example.com/v1?apikey=<REDACTED_GOOGLE_API_KEY>&q=test" } }
```

**3) Credentials block**

**BEFORE**

```json
{
  "credentials": {
    "clientId": "acme-app-123",
    "clientSecret": "shh-keep-me-safe",
    "webhookSecret": "whsec_9a8b7c",
    "aws": {
      "accessKeyId": "AKIAEXAMPLEKEY123456",
      "secretAccessKey": "wJalrXUtnFEMI/K7MDENG/bPxRfiCYzEXAMPLEKEY"
    }
  }
}
```

**AFTER**

```json
{
  "credentials": {
    "clientId": "acme-app-123",
    "clientSecret": "<REDACTED_CLIENT_SECRET>",
    "webhookSecret": "<REDACTED_WEBHOOK_SECRET>",
    "aws": {
      "accessKeyId": "<REDACTED_AWS_ACCESS_KEY_ID>",
      "secretAccessKey": "<REDACTED_AWS_SECRET>"
    }
  }
}
```

---

## 7) Do / Don’t

### Do

* Keep key names and nesting the same
* Use the placeholders above, consistently
* Lint the final file

### Don’t

* Remove whole objects/arrays unless they’re purely secrets
* Change data types (strings stay strings)
* Share raw files publicly without redaction

---

## 8) Success checklist

* [ ] Tokens, keys, passwords replaced with placeholders
* [ ] URL/connection creds and secret query params redacted
* [ ] JSON parses cleanly (no syntax errors)
* [ ] Structure intact; useful non-secret context kept
* [ ] Safe to share

---

## 9) Quick manual search terms

`apiKey` · `x-api-key` · `token` · `access_token` · `refresh_token` · `secret` · `clientSecret` · `password` · `webhook` · `hmac` · `signature` · `Authorization` · `apikey=` · `token=` · `sig=`

```
```
