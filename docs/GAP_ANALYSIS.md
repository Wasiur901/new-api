# GAP ANALYSIS — Master Directive vs. Current State

> Generated 2026-09-30 from `docs/CURRENT_STATE.md`. Each gap is tagged with the
> directive section it violates or addresses, and the phase that closes it.

## Legend

- 🟥 **Missing** — does not exist, must be built.
- 🟨 **Partial** — upstream capability exists but not configured/extended per spec.
- 🟩 **Present** — upstream already provides this; reuse rather than rebuild.

---

## A. Platform & topology (Directive §3–5, §63)

| Gap | Status | Phase |
|-----|--------|-------|
| Separate customer portal + admin CRM frontends | 🟥 | 1–3 |
| Backend-for-Frontend (BFF) control-plane | 🟥 | 1–2 |
| Modular control-plane (billing/finance/payment/provider/reconciliation) | 🟥 | 3–6 |
| PostgreSQL production DB (currently SQLite) | 🟨 | 1 |
| Redis enabled (currently not set) | 🟨 | 1 |
| Reverse proxy / TLS / ingress | 🟥 | 6 |
| Observability (OTel/Prometheus/Grafana/Loki) | 🟥 | 6 |
| Secrets manager | 🟥 | 1 (before prod) |
| `/control-plane`, `/portal`, `/infra`, `/automations`, `/docs` layout | 🟥 | 1 |

## B. Authentication & security (§6–9, §55, §61–62)

| Gap | Status | Phase |
|-----|--------|-------|
| Password login, sessions, audit | 🟩 (upstream) | 2 |
| Email verification | 🟩 (upstream `email_binding.go`) | 2 |
| TOTP 2FA (admin-mandatory) | 🟩 (upstream `twofa*.go`) — policy enforcement to add | 2 |
| Passkey/WebAuthn | 🟩 (upstream `passkey.go`) | 2 |
| Recovery codes | 🟨 | 2 |
| Role-based MFA policy (ADMIN/ROOT/FINANCE/SECURITY mandatory) | 🟨 | 2 |
| Strict RBAC roles (CUSTOMER/SUPPORT/FINANCE/OPERATIONS/ADMIN/ROOT) | 🟨 (upstream has configurable `authz_roles` + Casbin) | 2 |
| Step-up auth for sensitive admin actions | 🟩 (`secure_verification.go`) | 2 |
| Security headers (HSTS/CSP/SameSite/etc.) | 🟨 (`SESSION_COOKIE_SECURE` was explicitly **false** at runtime) | 2 |
| Rate limiting on auth/payment endpoints | 🟨 | 2 |
| Admin/customer portal logical separation | 🟥 | 2 |

## C. API keys (§10)

| Gap | Status | Phase |
|-----|--------|-------|
| Per-key model allowlist, quota, expiry, IP allowlist, rate limit | 🟩 (upstream token model) | 1 |
| Group / application / environment label | 🟨 | 1 |
| Last-used / total usage / total spend per key | 🟨 | 1 |
| Key rotation workflow | 🟨 | 1–3 |

## D. Provider: Baidu Qianfan (§11–12)

| Gap | Status | Phase |
|-----|--------|-------|
| Baidu adapter (OpenAI-compat) | 🟨 (upstream `baidu_v2`) | 1 |
| Fix `baidu_v2` `ChannelName = "volcengine"` bug | 🟥 | 1 |
| Model discovery / sync from provider (not hardcoded) | 🟥 | 3 |
| Model mapping table (internal↔provider↔public) | 🟥 | 3 |
| Usage extraction / error normalization / retry / upstream IDs | 🟨 | 1–3 |
| Provider health reporting | 🟨 | 6 |

## E. Pricing & billing (§13–18)

| Gap | Status | Phase |
|-----|--------|-------|
| Versioned pricing (list vs effective vs sell; CNY/BDT + FX) | 🟥 (upstream has only `pricing.go` defaults) | 3 |
| Exact decimal arithmetic for money | 🟥 | 3 |
| Per-request pricing-version reference | 🟥 | 3 |
| Real-time pre/post consumption charging | 🟨 (`quota_reserve.go`) | 3 |
| Immutable usage event + balance transaction + idempotency | 🟥 | 3 |
| Baidu price sync + approval workflow + audit | 🟥 | 3–5 |

## F. Payments: SSLCOMMERZ (§19–20)

| Gap | Status | Phase |
|-----|--------|-------|
| SSLCOMMERZ adapter / controller | 🟥 (no `sslcommerz` anywhere in repo) | 4 |
| Hosted flow + IPN + Order Validation API | 🟥 | 4 |
| Idempotent, never-double-credit wallet crediting | 🟥 | 4 |
| Payment state machine | 🟥 | 4 |
| Refund workflow | 🟥 | 4 |

## G. Wallet & accounting (§21–24)

| Gap | Status | Phase |
|-----|--------|-------|
| Immutable wallet transaction ledger | 🟥 | 3 |
| Double-entry ledger / COA / journals / trial balance | 🟥 | 5 |
| Prepaid accounting flow (liability, not revenue) | 🟥 | 5 |
| Daily finance close + reports | 🟥 | 5 |
| Baidu bill reconciliation + variance categories | 🟥 | 5 |

## H. Operations & automation (§28–41, §36–37)

| Gap | Status | Phase |
|-----|--------|-------|
| Structured JSON logs + correlation IDs | 🟨 | 6 |
| Metrics + dashboards | 🟥 | 6 |
| Deterministic job system (model/price/bill sync, close, backup) | 🟥 | 6 |
| OpenHands agent definitions (12 roles) | 🟥 | 6 |
| OpenHands automations (12 + skills) | 🟥 | 6 |
| Model health states | 🟨 | 6 |
| Abuse/cost-protection budgets + alerts | 🟨 | 3 |

## I. Licensing & compliance (§53–54)

| Gap | Status | Phase |
|-----|--------|-------|
| `LICENSE-COMPLIANCE.md` | 🟩 | 0 (done) |
| `PROVIDER_COMMERCIAL_COMPLIANCE.md` (Baidu resale authorization) | 🟩 | 0 (done — but resale rights remain **UNCONFIRMED**, a launch blocker until human review) |
| AGPL source-offer / attribution obligations documented | 🟩 | 0 (done) |

## J. Immediate hygiene (correctness, not features)

| Issue | Fix | Phase |
|-------|-----|-------|
| `TZ=Asia/Shanghai` in compose files | → `Asia/Dhaka` (**done**) | 0 |
| Plaintext `"baidu ai cloud"` credential file | → secrets manager / env | 0 |
| Plaintext `.admin-credentials` | → secrets manager | 0 |
| SQLite in production | → PostgreSQL | 1 |
| `SESSION_COOKIE_SECURE` off | → true in prod | 2 |
| `baidu_v2` channel name `"volcengine"` | → `"baidu_v2"` (**done**) | 0 |
| Uncommitted branding changes w/o ADR | → commit + ADR + attribution preservation | 0 |

---

## Priority ordering (matches directive phases)

1. **Phase 0** — discovery docs (this file), compliance/licensing docs, hygiene
   fixes, decide repo layout, establish CI.
2. **Phase 1** — PostgreSQL + Redis deployment, Baidu channel fix, key generation,
   routing, usage logs, minimal dashboard.
3. **Phase 2** — auth/RBAC/MFA/audit/headers/rate limits.
4. **Phase 3** — pricing + billing + wallet.
5. **Phase 4** — SSLCOMMERZ.
6. **Phase 5** — double-entry finance + reconciliation.
7. **Phase 6** — operations, observability, agents, automations.
8. **Phase 7** — hardening.