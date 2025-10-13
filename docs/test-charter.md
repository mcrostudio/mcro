# mcro — Test Charter v1 (Detection & Response)

**Status:** Adopted  
**Version:** 1.0  
**Last updated:** 2025-10-13  
**Owner:** Project Maintainers (Reliability)

---

## Purpose
Prevent high-impact, common workflow failures **before** they ship by statically linting exported workflow JSON from supported OTAs (n8n / Make / Zapier). Shift-left, partner-safe: **no execution**, **no secret custody**, **deterministic outputs**.

---

## Principles & Posture
- **Partner-safe.** Lint exported workflow JSON and committed samples/schemas only. No live API calls. No credentials/PII access.
- **Deterministic.** Same inputs ⇒ same findings. No network or time variance.
- **Humane.** Every finding states **why it matters** and a **one-step fix**. Suggestions over shaming.
- **Minimal blast radius.** Only guaranteed breakage **blocks**; everything else **nudges**.

---

## Scope (v1)
**In-scope**
- Exported workflow JSON; committed sample payloads; JSON Schemas.  
- Graph & mapping hygiene; schema & type drift; HTTP configuration; schedules; static security toggles (e.g., TLS verify flags).

**Out-of-scope**
- Live API execution; performance/load testing; runtime secret/PII handling; discovery of vendor-internal quotas/behaviour.

**Determinism**
- Offline, reproducible analysis; no network access during linting.

---

## Severities & Gate
- **BLOCKER** — Must fix (or waive with expiry) **before merge**. Required status check **fails**.
- **WARN** — Merge allowed, highlighted to fix soon. Required status check **passes**.
- **NOTE** — Advisory hygiene. **No gating**.

**Required status check label:** `Reliability Guard / lint`  
**JUnit output path:** `reports/reliability-guard.xml`  
**JUnit suite name:** `reliability-guard`

---

## Reporting Surface
Each finding includes:
- `rule_id` and short title  
- **Why it matters** (one sentence)  
- **One-step fix** (one sentence)  
- Location: `<workflow>#node:<id-or-name>`

Findings appear as a PR comment summary and as JUnit test cases (suite: `reliability-guard`).

---

## Waivers (time-boxed exceptions)
**File:** `.flowlint-waivers.yml`  
**Required fields:** `rule_id, owner, justification, scope, expires (ISO 8601)`  
**Defaults:** 14-day expiry; one renewal max. Expired waivers revert to active findings (**BLOCKER**).

**Waiver entry template**
    
    - rule_id: MCR-AREA-NNN
      scope: <path/to/workflow.json>#node:<id-or-name>
      owner: <email-or-username>
      justification: <short, factual reason>
      expires: <YYYY-MM-DD>

---

## AI Assist (optional, non-gating)
- **May do:** clarify explanations; propose one-step fixes; optionally draft **autofix patches** (unified diffs) behind an explicit flag.
- **Never does:** execute flows; access secrets/PII; auto-merge; alter gating outcomes. AI output is **advice only**; rules decide pass/fail.
- **Guardrails:** redact tokens; restrict context to the failing node + neighbours; log provenance (e.g., `suggested_by: ai@mcro`).

---

## Rule Set v1

### BLOCKER — fix before merge
1. **MCR-SCHEMA-001 — Downstream read of removed/renamed field**  
   *Schema drift leads to null/empty writes into sinks.*
2. **MCR-MERGE-001 — Join/merge on nullable/missing key without guard**  
   *Silent row drops with no quarantine/telemetry.*
3. **MCR-TYPE-001 — Nullability/type mismatch to required sink field**  
   *Nullable or wrong-type value mapped into a required/typed target.*
4. **MCR-DEADLETTER-001 — No failure path for non-replayable writes**  
   *Errors discarded; no dead-letter or compensation branch defined.*
5. **MCR-SECRET-001 — Literal secret in config/template/log string**  
   *Static token/API key present in the workflow definition or logs.*
6. **MCR-TLS-001 — TLS verification disabled for external HTTP**  
   *Insecure transport explicitly allowed in node configuration.*

### WARN — merge allowed, fix soon
1. **MCR-HTTP-001 — No retry/backoff or 429/Retry-After handling**  
   *Transient faults/rate limits will lose or flake events.*
2. **MCR-HTTP-002 — Pagination not implemented on list endpoints**  
   *Only first page fetched; datasets incomplete.*
3. **MCR-HTTP-003 — Missing idempotency keys where supported**  
   *Duplicate writes possible on retries/timeouts.*
4. **MCR-RATE-001 — Concurrency/throughput exceeds vendor quota**  
   *Configuration risks throttling under load.*
5. **MCR-SCHED-001 — Cron/schedule lacks timezone/DST clarity**  
   *Runs at unintended local times or double-fires on DST shifts.*
6. **MCR-ENUM-001 — Upstream enum/value-set drift unhandled**  
   *New values unrecognised; fall-through behaviour unclear.*
7. **MCR-OBS-001 — No drop counters or quarantine view**  
   *Discarded records are invisible to operations.*
8. **MCR-AUTH-002 — Likely auth scope/permission mismatch**  
   *Configured calls require scopes not granted in current auth.*

### NOTE — advisory hygiene
1. **MCR-GRAPH-001 — Unreachable nodes**  
   *Wired but never executed by the graph.*
2. **MCR-GRAPH-002 — Orphan outputs**  
   *Node emits data that no downstream consumer reads.*
3. **MCR-MAP-001 — Over-posting unnecessary fields to sinks**  
   *Payload bloat; avoidable governance risk.*
4. **MCR-STYLE-001 — Oversized logs/object dumps**  
   *Noisy output with potential PII over-share.*
5. **MCR-TEST-001 — Missing committed sample payloads/schemas**  
   *Complex expressions cannot be linted reliably.*
6. **MCR-WAIVER-001 — Waiver expiry within 7 days**  
   *Reminder to renew with justification or fix before expiry.*

---

## Message & JUnit Shape
- **PR comment composition:**  
  `**<rule_id> (<SEVERITY>)** — <short title>.  **Why it matters:** <one sentence>.  **One-step fix:** <one sentence>.  @<owner-if-known>`
- **JUnit mapping:**  
  - `testsuite name="reliability-guard"`  
  - `testcase name="<rule_id> @ <workflow>#node:<id-or-name>"`
  - **BLOCKER:** `<failure message="why + one-step fix"/>`  
  - **WARN:** `<skipped message="details"/>`  
  - **NOTE:** details in `<system-out>`

---

## Rule IDs
**Format:** `MCR-<AREA>-<NNN>`  
**Areas:** `SCHEMA, MERGE, TYPE, HTTP, RATE, SCHED, ENUM, OBS, AUTH, GRAPH, MAP, STYLE, TEST, WAIVER, TLS, SECRET`.

---

## Contributing (rule template)
When adding or modifying a rule, include:
    
    Rule ID: MCR-AREA-NNN
    Title: <short label>
    Area: <one of the Areas>
    Severity: BLOCKER | WARN | NOTE (include any escalation notes)
    Example (plain English): <concise real-world failure description>
    Detection (static, 1–2 lines): <how the linter knows; no network>
    One-step fix (1 line): <practical remediation>
    Tests: add minimal samples under /samples/ that reliably trigger the rule

---

## CI Intent
- **When to run:** On every Pull Request and on any change to workflow JSON or the waiver file.  
- **Outputs:** PR comment + JUnit XML at `reports/reliability-guard.xml`.  
- **Gate:** Mark **`Reliability Guard / lint`** as a required status check in branch protection.  
- **Determinism:** No network calls; same inputs produce the same findings.

---

## Done-When
- This file exists at `/docs/test-charter.md`.  
- Branch protection requires **`Reliability Guard / lint`**.  
- `.flowlint-waivers.yml` is documented in the repo root.  
- `/samples/` contains at least one workflow that triggers **1× BLOCKER** and **1× WARN** so the first PR exercises the checks.

---

## Glossary
- **PR (Pull Request):** Proposal to merge a set of changes into the main branch.  
- **Merge:** Accept a PR into the main branch (source of truth).  
- **Gate (status check):** Automated allow/block for merging, based on lint results.  
- **Waiver:** Time-boxed exception that records owner, scope, justification, and expiry.

---

## Review Cadence & Ownership
- **Review cadence:** Quarterly, or immediately after a severity-1 incident, vendor API change, or rule false-positive spike (>5% of PRs week-over-week).  
- **Change control:** Updates to this charter require approval from Reliability Maintainers; rule severity changes must include sample evidence under `/samples/`.

---
