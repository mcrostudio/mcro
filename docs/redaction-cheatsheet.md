# Redaction Quick Sheet (1-pager)

**Goal:** Strip secrets from JSON without breaking structure. **Target time:** ≤ 5 minutes.

---

## Must-hide
- API keys, bearer/refresh tokens, session cookies
- Passwords, client secrets
- Webhook/signing secrets
- DB creds in connection strings

## Keep (usually safe & useful)
- IDs, timestamps, enums, error codes, step names

## Placeholders
`<REDACTED_TOKEN>` `<REDACTED_API_KEY>` `<REDACTED_GOOGLE_API_KEY>` `<REDACTED_BASIC_CREDENTIALS>` `<REDACTED_CLIENT_SECRET>` `<REDACTED_WEBHOOK_SECRET>` `<REDACTED_USER>` `<REDACTED_PASSWORD>` `<REDACTED_AWS_ACCESS_KEY_ID>` `<REDACTED_AWS_SECRET>` `<REDACTED>`

---

## 5 steps
1) Duplicate file.  
2) Regex replace using patterns (Bearer/Basic, keys/secrets, URLs, query params).  
3) Manual sweep for `apiKey`, `token`, `secret`, `password`, `clientSecret`.  
4) Lint JSON:
~~~bash
jq . < file.json
~~~
5) Spot-check placeholders; share.

---

## Common regex (PCRE flavor)

> Enable case-insensitive where `(?i)` appears.

~~~regex
("Authorization"\s*:\s*")Bearer\s+[^"]+(")                  ->  $1Bearer <REDACTED_TOKEN>$2
("Authorization"\s*:\s*")Basic\s+[A-Za-z0-9+/=]+(")         ->  $1Basic <REDACTED_BASIC_CREDENTIALS>$2
("(?i)(api[_-]?key|x[-_]api[-_]key|key)"\s*:\s*")([^"]+)(") ->  $1<REDACTED_API_KEY>$3
("(?i)client[_-]?secret"\s*:\s*")([^"]+)(")                 ->  $1<REDACTED_CLIENT_SECRET>$3
("(?i)(access|refresh)[_-]?token"\s*:\s*")([^"]+)(")        ->  $1<REDACTED_TOKEN>$3
([a-z]+:\/\/)([^:@\/\s]+):([^@\/\s]+)@                      ->  $1<REDACTED_USER>:<REDACTED_PASSWORD>@
([?&](?:token|secret|sig|signature|key)=)[^&"\s]+           ->  $1<REDACTED>
~~~

**Backreferences:** Use `$1/$2` or `\1/\2`.  
**Validation:** JSON must parse and placeholders must be visible.
