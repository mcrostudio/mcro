# mcro — Design-Partner Brief (Free Reliability Scan)

**What this is:** a small, safe trial where we use mcro’s test-first, partner-safe tooling to review a handful of your automations (Zapier / Make / n8n) and show exactly how to make them harder to break—without touching production, credentials, or PII.

---

## Offer (free)
**Reliability scan of up to 10 workflows — JSON exports only.**  
We run offline static analysis with mcro’s lint rules, schema checks, and failure-mode heuristics, then return a concise, actionable report.

**Supported editors:** Zapier, Make, n8n (others by export).

---

## What you get (deliverables)
- **HTML Report** (shareable internally)
  - **Portfolio view:** 10/10 workflows with severity + risk ranking
  - **Per-workflow findings:** rule hit, why it matters, **suggested fix**
  - **Deep-links** back to your editor at the exact step/node
  - **Cross-cutting risks:** deprecations, rate-limit hotspots, version drift
- **30-minute review call** to align on top fixes and next steps
- **Pilot proposal** (optional): small, measurable reliability pilot

---

## Boundaries (safety & scope)
- **No secrets** (no tokens, passwords, keys) and **no PII**
- **No execution, no vendor API calls, no write access**
- **Offline analysis only** on your **exported JSON files**
- **NDA optional** — our default process is already safe; happy to sign yours

**Data hygiene:** minimal intake; analysis artifacts are stored short-term for the engagement only, then deleted. See `/docs/security-data-handling.md`.

---

## What we look for (rule families)
- **Deprecations & version drift** (apps, endpoints, SDKs)
- **Timeouts, retries, and 429/5xx loops** (rate-limit hygiene)
- **Missing guards/checkpoints** on long chains and brittle branches
- **Webhook fragility** (idempotency, duplicate trigger protection)
- **Schema assumptions** (nullability, pagination, array/object shape)
- **Auth edges** (shared connections, “blast radius” nodes)
- **Editor-specific footguns** (e.g., unsafe defaults, silent truncation)

Each finding includes: **impact**, **why it breaks**, **how to fix**, **deep-link**.

---

## What we ask (if you’re happy)
- **Two anonymised screenshots** of the report (portfolio + one finding), and
- **A 1–2 sentence quote** we can publish (no names or client data required)

Example: “mcro flagged three silent failures we’d have missed; the deep-links made fixes a 10-minute job.”

---

## Pilot success criteria (for a follow-on)
If the brief is useful, we’ll propose a tiny, time-boxed pilot with clear, measurable outcomes. Typical criteria (pick 3–4):

- **≥3 preventive checks** wired into your PR/merge gate for risky patterns  
- **0 open deprecation warnings** across the scanned set  
- **Guard coverage ≥80%** on high-risk nodes (timeouts/retries/version pins)  
- **Incident class eliminated** (e.g., webhook 429 loop = 0 occurrences over 30 days)

Pilot mechanics are **partner-safe**: mcro adds read-only checks + deep-links; your team applies fixes in Zapier/Make/n8n.

---

## Process (step-by-step)
1) **Intake:** You export up to 10 workflows as JSON (redacted; no secrets/PII).  
2) **Scan:** We analyze offline with mcro’s rules + heuristics.  
3) **QA:** We scrub identifiers and validate all deep-links.  
4) **Deliver:** You receive the HTML report.  
5) **Review (30 mins):** Walkthrough + agree pilot hypothesis (optional).

---

## Why teams do this
- **Value first:** concrete fixes that prevent real mistakes and outages  
- **No risk:** no live access, no credentials, no execution  
- **Partner-safe:** we point back to vendor editors; we don’t replace them  
- **Reusable proof:** internal ammo for reliability investment; public quote/screens (anonymised) for us

---

## Eligibility & prep
- You maintain **≥20 active automations** or workflows with **customer or revenue impact**  
- You can **export JSON** from your editor(s)  
- Optional: share short **incident notes** to map “would-have-prevented” callouts

---

## Contact
**mcro** — partner-safe reliability for automation teams  
Email: founder@mcro.studio

---

### “Done when…”
A friendly target reads this page and says **“I’d do this”** with **no new questions** about scope, safety, or deliverables.
