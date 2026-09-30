# IMPLEMENTATION PLAN — Milestones

> Generated 2026-09-30. Follows directive §64–65. Each phase has acceptance
> criteria and must not be declared complete until criteria are met (§65).

## Guiding sequencing rules

- Do **not** begin a large rewrite before discovery (Phase 0) is accepted.
- Prefer adapter/control-plane extensions over invasive upstream edits (§3, §50).
- Establish CI **before** high-risk changes (§65 step 7).
- Billing/payment/ledger/permission changes attract extra targeted tests (§48).

---

## PHASE 0 — DISCOVERY (complete; awaiting operator sign-off)

**Deliverables**
- [x] `docs/CURRENT_STATE.md` ✅
- [x] `docs/GAP_ANALYSIS.md` ✅
- [x] `docs/ARCHITECTURE.md` ✅
- [x] `docs/IMPLEMENTATION_PLAN.md` ✅ (this file)
- [x] `docs/LICENSE-COMPLIANCE.md` ✅ (AGPL §7 attribution + §4 modifications log)
- [x] `docs/PROVIDER_COMMERCIAL_COMPLIANCE.md` ✅ (Baidu resale authorization — **launch blocker** if unclear)
- [x] First ADR (`docs/adr/0001-extend-do-not-rewrite-new-api.md`) ✅
- [x] `.agents/agents/` (12 specialized agents) + `.agents/skills/` (10 project skills) ✅
- [x] Hygiene: `TZ=Asia/Dhaka` in both compose files ✅; `baidu_v2` `ChannelName` fix (`"volcengine"` → `"baidu_v2"`) ✅
- [ ] Hygiene (open): move plaintext Baidu credential out of `~/Work/"baidu ai cloud"`; commit branding to a feature branch; decide fork strategy (rebase onto rc.40?).

**Acceptance**: docs approved by operator; secrets not in plaintext; TZ corrected.

---

## PHASE 1 — CORE PLATFORM

**Deliverables**
- PostgreSQL-backed New API deployment + Redis (`/infra`). Set `SQL_DSN`, `REDIS_CONN_STRING`.
- Fix `baidu_v2` `ChannelName` bug; verify Baidu Qianfan connectivity via V2 endpoint.
- Customer API-key creation (reuse New API tokens); per-key model/quota/IP/rate-limit.
- Basic model routing (ERNIE via Baidu channel).
- Usage logs per request.
- Minimal customer dashboard (balance / usage).

**Acceptance**: customer can create key → call Baidu-backed model → request is
correctly attributed → usage recorded. No SQLite in prod.

---

## PHASE 2 — AUTHENTICATION & SECURITY

**Deliverables**
- Custom branded login around New API auth.
- Email verification; TOTP (mandatory for ADMIN/ROOT/FINANCE/SECURITY); Passkey; recovery codes.
- Session management + revocation; RBAC roles; audit logs; security headers (HSTS/CSP/SameSite/Secure cookies); rate limiting (login/register/reset/2FA/email/key/payment).

**Acceptance**: admins require MFA; sessions revocable; security events auditable;
`SESSION_COOKIE_SECURE=true`.

---

## PHASE 3 — BILLING

**Deliverables**
- Pricing catalog + versioned pricing (provider list / effective cost / sell price) + CNY/BDT FX.
- Exact-decimal usage costing; immutable usage events; wallet ledger; quota enforcement; per-request pricing-version reference.
- Baidu price sync (source priority §14) + approval workflow + audit.
- Usage dashboard with filters (date/key/model/status/app).

**Acceptance**: every paid request independently recalculable from immutable source data.

---

## PHASE 4 — SSLCOMMERZ

**Deliverables**
- Sandbox integration; server-side IPN + Order Validation API; idempotency; wallet crediting; payment history; refund workflow; payment state machine.

**Acceptance**: duplicate callbacks cannot duplicate credit. Sandbox suite passes
before any production credentials.

---

## PHASE 5 — FINANCE

**Deliverables**
- Chart of accounts; double-entry ledger; journals; trial balance; P&L; balance sheet; daily close; payment reconciliation; Baidu bill reconciliation (variance categories §24).

**Acceptance**: journal always balances; every total drills into source records;
no LLM-fabricated numbers (UNRECONCILED labels where data missing).

---

## PHASE 6 — OPERATIONS

**Deliverables**
- Metrics/Grafana/alerts; backups + restore test; runbooks; OpenHands agent
  definitions (12 roles) + skills (10) + automations (12); deterministic job system.

**Acceptance**: backups restore successfully; deterministic jobs idempotent w/ run history.

---

## PHASE 7 — HARDENING

**Deliverables**
- Load testing; security review; penetration testing; billing edge-case testing; DR test; provider/payment failure simulation.

**Acceptance**: all release-blocking issues resolved before production launch.

---

## Near-term next actions (after operator confirms Phase 0 outputs)

1. Confirm fork strategy: rebase the fork onto upstream `v1.0.0-rc.40` before any
   new code (we are 4 RCs behind), or pin rc.36 deliberately and document why.
2. Commit existing branding changes to a feature branch with attribution preserved
   (NOTICE §7) and an ADR.
3. Move the plaintext Baidu credential to a secret store/env; set `TZ=Asia/Dhaka`.
4. Rebuild the backend binary so `web/dist` (with the rebrand) is embedded, and
   restart/sync the running instance (`/home/wasi/Work/new-api`, port 3000).
5. Scaffold `/control-plane`, `/portal`, `/infra`, `/automations`, CI, then Phase 1.