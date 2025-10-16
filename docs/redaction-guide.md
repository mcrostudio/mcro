Got you. Here’s a single Markdown file you can drop into your repo (e.g., `/docs/redaction-guide.md`).

```markdown
# Redaction Guide

**mcro — Share JSON safely without breaking structure**

---

## 1) Purpose & audience

This guide shows anyone (including non-engineers) how to remove secrets from workflow/export JSON while keeping the JSON valid and useful for debugging. **Target time:** ≤ 5 minutes.

---

## 2) Scope — what to redact vs keep

### Must redact (always)
- **Authentication:** API keys, bearer/refresh tokens, session cookies  
- **Passwords and client secrets:** OAuth `client_secret`, Basic auth `user:pass@`  
- **Webhook/signing secrets, HMAC seeds**  
- **Credentials in connection strings or URLs:** usernames, passwords, secret query params

### May redact (policy/context)
- **Personal data:** emails, phone numbers, addresses, names  
- **Internal endpoints, internal IDs** if sensitive  
- **Proprietary field names** (rare; keep structure if possible)

### Keep (usually not secret; often vital)
- Non-secret IDs (e.g., order IDs), timestamps, enums, error codes, step names

### Principles
- **Preserve shape** (keys, arrays, nesting)  
- **Replace values only** with standard placeholders  
- **Leave parsable JSON** (lint before sharing)

---

## 3) Standard placeholders

Use **ALL-CAPS angle-bracket** tokens so it’s obvious what changed.

| Placeholder | Use for |
|---|---|
| `<REDACTED_TOKEN>` | Bearer/OAuth tokens, session tokens |
| `<REDACTED_API_KEY>` | Generic API keys |
| `<REDACTED_GOOGLE_API_KEY>` | Google API keys (AIza…) |
| `<REDACTED_BASIC_CREDENTIALS>` | Basic header credentials |
| `<REDACTED_CLIENT_SECRET>` | OAuth client secret |
| `<REDACTED_WEBHOOK_SECRET>` | Webhook/signing/HMAC secrets |
| `<REDACTED_USER>` | Username in URLs/connection strings |
| `<REDACTED_PASSWORD>` | Password in URLs/connection strings |
| `<REDACTED_AWS_ACCESS_KEY_ID>` | AWS Access Key ID (AKIA…/ASIA…) |
| `<REDACTED_AWS_SECRET>` | AWS Secret Access Key |
| `<REDACTED>` | Catch-all for secret query values |

---

## 4) Five-minute flow

1. **Duplicate** your JSON file (work on the copy).  
2. **Open Regex find/replace** in your editor (e.g., VS Code: toggle `.*`).  
3. **Run the patterns** in §5 (one category at a time).  
4. **Manual sweep** for obvious keys: `apiKey`, `token`, `secret`, `password`, `clientSecret`.  
5. **Validate JSON (lint).** CLI example:  
   ~~~bash
   jq . < file.json
   ~~~
6. **Spot-check:** secrets gone, structure intact, placeholders present.  
7. **Backreferences:** some editors use `$1/$2`; others use `\1/\2`.

---

## 5) Find-and-replace patterns (PCRE-style)

> Enable case-insensitive where `(?i)` appears.

### A) Authorization headers

**Bearer**
~~~regex
FIND:    ("Authorization"\s*:\s*")Bearer\s+[^"]+(")
REPLACE: $1Bearer <REDACTED_TOKEN>$2
~~~

**Basic**
~~~regex
FIND:    ("Authorization"\s*:\s*")Basic\s+[A-Za-z0-9+/=]+(")
REPLACE: $1Basic <REDACTED_BASIC_CREDENTIALS>$2
~~~

**Token token=…**
~~~regex
FIND:    ("Authorization"\s*:\s*")Token\s+token=[^"]+(")
REPLACE: $1Token token=<REDACTED_TOKEN>$2
~~~

### B) Generic key/secret fields

**Any apiKey field**
~~~regex
FIND:    ("(?i)(api[_-]?key|x[-_]api[-_]key|key)"\s*:\s*")([^"]+)(")
REPLACE: $1<REDACTED_API_KEY>$3
~~~

**Client secret**
~~~regex
FIND:    ("(?i)client[_-]?secret"\s*:\s*")([^"]+)(")
REPLACE: $1<REDACTED_CLIENT_SECRET>$3
~~~

**Access/refresh tokens**
~~~regex
FIND:    ("(?i)(access|refresh)[_-]?token"\s*:\s*")([^"]+)(")
REPLACE: $1<REDACTED_TOKEN>$3
~~~

**Webhook/signing secrets**
~~~regex
FIND:    ("(?i)(webhook|sign(ing)?|hmac)[_-]?(secret|key)"\s*:\s*")([^"]+)(")
REPLACE: $1<REDACTED_WEBHOOK_SECRET>$3
~~~

### C) Cloud/provider-specific

**Google API key**
~~~regex
FIND:    AIza[0-9A-Za-z_\-]{35}
REPLACE: <REDACTED_GOOGLE_API_KEY>
~~~

**AWS Access Key ID**
~~~regex
FIND:    (A(K|S)IA[0-9A-Z]{16})
REPLACE: <REDACTED_AWS_ACCESS_KEY_ID>
~~~

**AWS Secret Access Key (fielded)**
~~~regex
FIND:    ("(?i)secret(access)?key"\s*:\s*")([^"]+)(")
REPLACE: $1<REDACTED_AWS_SECRET>$3
~~~

### D) URLs & connection strings

**URL credentials (no lookbehind; broad support)**
~~~regex
FIND:    ([a-z]+:\/\/)([^:@\/\s]+):([^@\/\s]+)@
REPLACE: $1<REDACTED_USER>:<REDACTED_PASSWORD>@
~~~

**DB/queue connection strings**
~~~regex
FIND:    (postgres|mysql|mongodb|redis|amqp):\/\/([^:@\/\s]+):([^@\/\s]+)@
REPLACE: $1://<REDACTED_USER>:<REDACTED_PASSWORD>@
~~~

**Secret query params (value only)**
~~~regex
FIND:    ([?&](?:token|secret|sig|signature|key)=)[^&"\s]+
REPLACE: $1<REDACTED>
~~~

---

## 6) Example set (separate JSON files)

See `/docs/redaction-examples/`:

- `headers.before.json` → `headers.after.json`  
- `url.before.json` → `url.after.json`  
- `creds.before.json` → `creds.after.json`

---

## 7) Do / Don’t

### Do
- Keep key names and nesting the same.  
- Use the standard placeholders consistently.  
- Lint the final file.

### Don’t
- Remove whole objects/arrays unless they’re purely secrets.  
- Change data types (strings stay strings).  
- Share raw files in public channels without redaction.

---

## 8) Quick checklist (success criteria)

- [ ] All tokens, keys, and passwords replaced with placeholders  
- [ ] URLs/connection strings have redacted `user:password@` and secret query params  
- [ ] JSON validated successfully (no syntax errors)  
- [ ] Structure intact; non-secret IDs and timestamps kept  
- [ ] File is safe to share

---

## 9) Appendix — quick manual search terms

**Plain (non-regex) search:**  
`apiKey`, `x-api-key`, `token`, `access_token`, `refresh_token`, `secret`, `clientSecret`, `password`, `webhook`, `hmac`, `signature`, `Authorization`, `apikey=`, `token=`, `sig=`
```
