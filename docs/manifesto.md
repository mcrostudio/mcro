# mcro — Partner-Safe Efficiency Manifesto
_v1.0 · 13 Oct 2025 · Audience: CTOs, platform vendors, ops leads, workflow builders_

**TL;DR**  
mcro is a **partner-safe efficiency studio** for **n8n, Make, and Zapier**. It helps beginners, everyday builders, and experts **build, fix, and optimize** workflows faster—**without replacing vendor editors, executing customer flows, or holding secrets**. mcro compiles intent to engine-native JSON, detects risks, proposes **minimal, reversible patches**, and **deep-links back** to the exact node/field in vendor UIs.  
- **n8n:** transactional **apply via official APIs** with preconditions and a signed rollback bundle.  
- **Zapier/Make:** **StepCards + Manual Loader** (human-applied) with an **overlay-only** Browser Helper.

---

## Partner-Safe Pledge (non-negotiables)
- **No execution** of customer workflows (no run/replay/orchestration).  
- **No secret custody** (store **names/handles only**; BYO-vault attestation optional).  
- **No rival editor** (mcro **always deep-links back** to vendor editors).  
- **No UI automation or scraping** (Browser Helper highlights only; never clicks or fills).  
- **No TOS workarounds** (no quota evasion; no shadow automation).  
- **Offline/local-first analysis** (no vendor API calls during analysis; n8n apply is opt-in via official APIs).

---

## 1) Mission & Position
mcro is the **efficiency layer** for the OTA ecosystem. It makes n8n/Make/Zapier easier for **everyone**—a gateway for newcomers, a time-cutter for pros, and a reliability amplifier for vendors. We **partner**, not compete: users design and run in vendor tools; mcro streamlines the journey.

---

## 2) What mcro *is* (platform overview)
- **Build Studio** — compile intent to **engine-valid JSON** (stable node IDs, typed edges); generate **Golden Tests** alongside changes; open any node/field in the vendor editor with one click.  
- **Fix Studio** — diagnose schema drift, brittle nodes, silent data-loss; propose **RFC-6902** patches with exact JSON pointers, blast-radius previews, **partial accept** and **undo**.  
- **Optimize Studio** — flag retries/idempotency gaps, quota traps, payload bloat, unbounded arrays; show **P50/P95/cost estimates** with assumptions labeled.  
- **Skills Toggle** — **Apprentice / Builder / Expert** adjusts UI density, lint strictness, test verbosity, and inline explainers.  
- **Agency Mode** — client workspaces, handoff locks, Quick-Apply (≤100 flows), portfolio view, branded evidence.  
- **CLI + CI** — headless checks, deterministic outputs, merge-gates, audit-ready artifacts.

---

## 3) What mcro *is not*
- Not an executor/orchestrator of customer flows.  
- Not a secret vault or processor of live credentials.  
- Not a rival editor or UI scraper/auto-clicker.  
- Not a quota-evasion or TOS-bypass tool.

---

## 4) Core primitives (why it scales and stays safe)
- **FlowIR (neutral internal model):** nodes, edges, typed contracts; **secrets as handles** (never values).  
- **Content-addressed artifacts:** every flow, patch, fixture, schema, evidence has a **hash**.  
- **Signed actions:** signer identity, timestamp, purpose, artifact hashes; optional reviewer co-sign.  
- **Unified error taxonomy:** Auth / Permission / Quota / Transient / Data-shape / Validation / Vendor bug / Logic.  
- **Privacy budgets + redaction:** PII rules attach to fixtures; evidence auto-redacts.

---

## 5) Engine Adapters SDK (vendor-friendly foundation)
**Adapter contract (conceptual):** `parse → emit → lint → diff → capabilities → apply → openInVendor`.  
- **Day-1 scope:**  
  - **n8n:** full read/write via **official APIs**; **transactional apply** (preconditions + tests + rollback bundle).  
  - **Zapier & Make:** read/inspect where APIs allow; **no write** → **StepCards + Manual Loader** fallback.

---

## 6) Deterministic test harness & fixtures
- **Determinism:** time freeze, seeded RNG, canonical JSON, locale/encoding normalization.  
- **Fixture capture:** webhook catcher; paste/upload JSON/HAR; **preview redaction**; stable hashes.  
- **Stubs library (Top-30 connectors):** pinned versions; happy-paths and edges (429/401; pagination; nulls; shape drift).  
- **Invariants & property checks:** e.g., “`total` is number ≥ 0”, array length ≤ N, field exists.  
- **Green-Check Contract:** a flow is **Verified** when local tests pass on stubs (badge shown in Studios/Archive/Evidence).

---

## 7) OTA Archive (read-only inventory)
- **Two-click Link & Discover:** index Zaps/Scenarios/Workflows (ids, names, owners, labels, versions, last-run where available).  
- **Patch Preview Everywhere:** blast radius, affected nodes, linked tests, **open-in-vendor** shortcuts.  
- **Security posture:** least-privilege scopes; **no secrets imported**; read-only.

---

## 8) Apply paths by engine (safety rails)
- **n8n — transactional apply:** preconditions (base hash), staged patch, run attached tests, mint **signed rollback bundle**. Trivial drift → **auto-rebase**; otherwise stop with **StepCards**. Full audit trail linked to evidence.  
- **Zapier & Make — StepCards + Manual Loader:** ordered cards with exact field highlights, expected values, and deep-links. **Manual Loader** (two-click: load patch/SAA → generate StepCards).  
- **Browser Helper (overlay-only):** visually highlights target fields; never writes the DOM.

---

## 9) Schema Registry (lite) & Drift Sentry
- Auto-derive JSON Schemas from fixtures; detect **shape diffs** over time.  
- Raise “**Data-shape changed**” issues with pinpointed fields; one-click to add checks to tests.  
- **Version Locker & Drift Sentry:** pin engine/connector versions; produce impact reports; safe rebase or StepCards fallback.

---

## 10) Evidence system (locker, packs, repo push)
- **Evidence Locker:** immutable store of tests, diffs, signatures, reviewers, hashes, environment, results.  
- **Evidence Pack v1:** includes context (engine/flow/env/versions/timestamps), artifact hashes (flow/patch/fixtures/schemas), test results, signatures (author/reviewer/key/time), privacy notes (redactions/classifications), and deep-links.  
- **Repo Push:** signed commit with content hashes; optional PR checks gate on lints/tests/preconditions.

---

## 11) Observe & Govern
- **Coverage/Risk Heatmap:** % with tests; open deprecations; drift detected; last evidence date; “ready-to-upgrade” score.  
- **Ready-to-Upgrade Checklist:** all flows Green-Check; no open drift; versions pinned; rollback bundles minted.  
- **Audit exports:** CSV/JSON of changes, signers, reviewers, env hops.

---

## 12) Environments & promotion gates
- Map **dev → stage → prod** per engine.  
- **Two-person approval** for prod applies; each hop mints a **signed rollback**.  
- Optional **change-freeze** windows.

---

## 13) Skills Toggle
**Apprentice / Builder / Expert** changes:  
UI density · lint strictness · test verbosity · inline explainers · temporary elevate (auto-revert & record) · per-project overrides.

---

## 14) Agency Mode
- **Client Workspaces** (isolation; simple RBAC).  
- **Handoff Lock** (freeze delivery; further edits become patches).  
- **Quick-Apply** selection (≤100 flows) with **per-flow rollback** and branded evidence.  
- **Portfolio View:** clients × flows grid with risk badges.

---

## 15) Browser Helper (overlay-only)
Highlights exact inputs referenced by StepCards; optional clipboard snippets. **Never** automates the DOM; CSP-respecting; per-tenant opt-in.

---

## 16) Vendor loop (why vendors win)
- **Repro Pack export:** minimal flow, redacted fixture, expected vs actual, signatures.  
- **Ticket Helper:** pre-filled issue text and links; attach Evidence/Repro pack.  
- **Deflection Counter:** patches applied vs. vendor tickets avoided.  
**Net effect:** fewer breakages, faster upgrades, and **higher OTA adoption**—mcro is a channel, not a competitor.

---

## 17) Trust & compliance (lite Day-1)
- **BYO-Vault attestation:** record vault type (Hashi/AWS/GCP/Azure); mcro holds **names**, not values.  
- **GDPR/PII pack:** PII lint rules, redaction policies, export masking controls.  
- **Org controls:** RBAC, audit log export (SSO/SAML & SCIM on Enterprise).  
- **Trust Center (mini):** status ping, last incident note, data-flow one-pager, downloadable DPA, **Partner Pledge**.

---

## 18) Open SAA v0 (signed automation artifact)
A small, content-addressed envelope for **flow + patch + tests** reused by Evidence, Repo Push, and Manual Loader. Fields include: SAA version, flow hash/URI, patch (RFC-6902 + base hash), tests (ids/hashes/URIs), schemas (paths/hashes), fixtures (ids/privacy/hashes), signatures (signer/key/time/checksums), metadata (engine/project/env/notes).

---

## 19) Billing & entitlements (Day-1 model)
- **Free / Read-Only:** index ≤ _N_ flows; lint + diff; local tests; StepCards preview; watermarked evidence; no Repo Push.  
- **Pro (per seat):** Free + **n8n apply + rollback**, Repo Push, CLI/CI, Skills Toggle, Green-Check badge, Quick-Apply (≤100).  
- **Agency (seat + clients):** Client Workspaces, Handoff Lock, branded evidence, Portfolio View, priority drift alerts.  
- **Enterprise:** SSO/SAML, SCIM, policy packs, data-residency toggle, Private tenancy, SLAs.  
**Enforcement:** entitlement guards with graceful read-only degrade; audited plan changes.

---

## 20) Capability matrix (Day-1 clarity)

| Capability              | n8n                          | Zapier     | Make       |
|-------------------------|------------------------------|------------|------------|
| Read / Index            | ✅                           | ✅         | ✅         |
| Write / Apply           | ✅ via official APIs         | ❌         | ❌         |
| Deep-links              | ✅                           | ✅         | ✅         |
| StepCards / Manual Load | ✅ (fallback)                | ✅         | ✅         |
| Versions / Last-run\*   | ✅                           | Partial    | Partial    |

\* where surfaced via public APIs.

---

## 21) Day-1 ship list (tight scope)
Adapters SDK + FlowIR · Build/Fix/Optimize Studios · Deterministic harness/fixtures/stubs/invariants · Schema Registry (lite) · Drift Sentry · OTA Archive with Patch Preview · **Apply:** n8n transactional; Zapier/Make StepCards + Manual Loader + Browser Helper · Evidence Locker + Evidence Pack v1 + Repo Push · Env Maps & Promotion Gates (signed rollback) · Observe & Govern (heatmap, checklist, exports) · Skills Toggle (A/B/E) · CLI & CI templates · Agency Mode basics · Billing & Entitlements wired · Trust Center (mini) + Partner Pledge + GDPR/PII pack (lite).

---

## 22) Data & security posture (details)
- **Processing model:** local/offline by default; **in-memory**; ephemeral temp files **purged on exit**.  
- **Inputs:** redacted workflow JSON; Redaction Guide and preview-redaction provided.  
- **Storage:** mcro does **not** persist workflow content or secrets by default; logs exclude payloads.  
- **Analytics/training:** customer content is **never** used.  
- **DPA stance (non-legal):** with local, redacted, offline scans mcro is typically **not** a data processor. If non-redacted data is shared for support, mcro will sign a DPA.

---

## 23) Reporting, severities & waivers
- **Report:** single-file HTML; filters; per-finding detail; **copyable deep-links**; semantic diff; blast-radius preview.  
- **Severities:** Critical / High / Medium / Low (conservative defaults; rationale visible).  
- **Waivers:** allowed but **time-boxed by default** (e.g., 30 days); all waivers logged and visible in reports/evidence.

---

## 24) Versioning & change control
- **SemVer** for rulepacks/adapters.  
- Every report shows rulepack + adapter versions and the **commit hash** that produced it.  
- **Changelog** maintained at `/docs/CHANGELOG.md`.

---

## 25) Ethics & responsible disclosure
- **TOS respect** is explicit; mcro refuses quota-evasion use cases.  
- Security findings are reported privately to vendors; no public disclosure without coordination.  
- Redaction-first samples; minimal reproduction; no customer identifiers in shared artifacts.

---

## 26) Who mcro serves (at a glance)
- **New builders:** guardrails, explainers, StepCards → friendly gateway into n8n/Make/Zapier.  
- **Everyday users:** faster iteration, fewer breakages, clear diffs and evidence.  
- **Experts & agencies:** drastic time cuts, transactional apply on n8n, portfolio governance.  
- **Vendors:** fewer tickets, cleaner upgrades, higher adoption and revenue.

---

### Five hard checkboxes (guarantees)
- [x] **No execution** of workflows  
- [x] **No secret custody** (handles only; BYO-vault attestation)  
- [x] **No rival editor; always deep-link back**  
- [x] **Offline analysis by default** (no vendor calls during analysis)  
- [x] **Apply only via official paths** (n8n API + rollback) or **manual StepCards**

---

**Linked references (repo paths)**  
`/docs/test-charter.md` · `/docs/security-data-handling.md` · `/docs/redaction-guide.md`

_Signed: Alexander Montagu-Scott - Founder 
