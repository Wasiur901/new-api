# CURRENT STATE — Phase 0 Discovery

> Generated 2026-09-30. This document records the *actual* repository state as
> discovered, not a target state. It is the baseline for GAP_ANALYSIS.md.

## 1. Repository layout on this machine

Two related directories exist under `~/Work`:

| Path | What it is |
|------|------------|
| `~/Work/new-api-fork` | Git checkout of upstream `QuantumNous/new-api`, pinned to tag `v1.0.0-rc.36`, with **uncommitted** frontend branding changes. |
| `~/Work/new-api` | A *deployed instance*: a compiled 155 MB binary plus runtime data (`.env`, `.admin-credentials`, SQLite DB, logs). **Not a git repository.** |

There is **no** top-level "platform" repository yet — no `/control-plane`, `/portal`,
`/infra`, or `/automations` modules exist. The master directive's target
architecture has not been scaffolded.

Additional unrelated projects in `~/Work` (opencode-web, retail-ai, ghost-studio,
JIT, etc.) are out of scope for this directive.

## 2. Upstream New API version

- Local fork is pinned at tag **`v1.0.0-rc.36`** (commit `ea7cb0b`).
- The clone is **shallow / grafted** (single commit, `git log` shows only one entry).
- Latest upstream release on GitHub is **`v1.0.0-rc.40`** (published 2026-09-21).
  → The fork is **4 release candidates behind** upstream.
- Module path: `github.com/QuantumNous/new-api`, `go 1.25.1`.

## 3. Modifications already made (uncommitted)

`git status` / `git diff --stat` show only **frontend re-branding** changes, nothing
on the Go backend:

```
web/bun.lock
web/index.html
web/package.json
web/public/favicon.ico
web/src/features/home/components/sections/features.tsx
web/src/features/home/components/sections/hero.tsx
web/src/features/system-settings/maintenance/config.ts
web/src/features/system-settings/maintenance/header-navigation-section.tsx
web/src/hooks/use-top-nav-links.ts
web/src/lib/nav-modules.ts
web/src/styles/index.css
web/src/styles/theme.css   (+279/-…)
web/public/loop-favicon.png (untracked)
```

The hero changes remove a "Docs" button and some `useStatus()` wiring; the theme.css
changes are extensive. This is an early, partial re-brand (new favicon
`loop-favicon.png` and `loop-logo.png`, "Bungee"/"Gabarito" display typography) whose
brand name is **tentatively "LOOP" / "dhaka-threads"** — the only in-repo hint is the
comment `/* Brand display face (LOOP / dhaka-threads) for primary page titles. */` in
`web/src/styles/index.css`. The `<title>` and default system name still read
"New API".

The Go backend carries **one** uncommitted change from upstream `v1.0.0-rc.36`:

```diff
-relay/channel/baidu_v2/constants.go: var ChannelName = "volcengine"
+relay/channel/baidu_v2/constants.go: var ChannelName = "baidu_v2"
```

This fixes the copy-paste channel name so the Baidu V2 adapter registers under its
own channel name (referenced in section 7).

## 4. Frontend architecture

- Framework: **React 19 + TypeScript**, built with **Rsbuild 2** (not Next.js).
- Routing/data: TanStack Router / Query / Table, Zustand, Base UI, Tailwind CSS 4.
- Package manager: **Bun** (preferred per repo AGENTS.md). `bun.lock` present.
- i18n: i18next (`en`, `zh`, `zh-TW`, `fr`, `ru`, `ja`, `vi`).
- The directive specifies a separate customer/portal frontend (Next.js). The current
  frontend is the upstream New API admin/console SPA. **No separate customer portal,
  BFF, or admin CRM exists yet.**

## 5. Database

- **Production/running instance uses SQLite** (`SQLITE_PATH` in `.env`,
  `data/one-api.db`, ~918 KB) — this violates the directive's "never SQLite in
  production" rule.
- Upstream supports SQLite, MySQL, PostgreSQL (and ClickHouse for the log DB).
- `docker-compose.yml` ships `postgres:15`, `redis:latest`, and `new-api` services.
- Redis is **not enabled** on the running instance (`REDIS_CONN_STRING not set`).

## 6. Deployment method (current)

- The gateway is running as a **bare standalone binary**:
  `~/Work/new-api/new-api --port 3000 --log-dir ~/Work/new-api/logs`.
- No systemd unit is installed (`new-api.service` exists in the fork tree but
  `systemctl status new-api` → not found).
- No Docker compose stack is running for this deployment. Docker 29 is installed.
- Startup log shows a *fresh initialization* (no root user existed, 0 dashboard rows),
  then the process was terminated ~2 minutes later. The instance is essentially an
  initial, unconfigured deployment.

## 7. Baidu / Qianfan integration

- Upstream ships **two** built-in Baidu channels under `relay/channel/`:
  - `baidu/` (adaptor, constants, dto, relay-baidu)
  - `baidu_v2/` — OpenAI-compatible V2 adapter using `/v2/chat/completions`.
- `baidu_v2/constants.go` has a copy-paste bug:
  `var ChannelName = "volcengine"` (should be `"baidu_v2"`). **Fixed** in this
  working tree (uncommitted) as part of Phase 0 hygiene.
- `baidu_v2.ModelList` hardcodes ~24 ERNIE/DeepSeek model IDs, confirming the
  directive's concern that the model list is hardcoded rather than synced from the
  provider.
- No provider pricing sync, bill sync, or reconciliation logic specific to Baidu
  exists beyond New API's generic channel/pricing machinery.

## 8. Payment integration

- Upstream top-up controllers exist for **Creem, Stripe, Waffo, Pancake** and
  subscription payments for **Stripe, Creem, Epay**.
- **SSLCOMMERZ is not integrated at all** (`grep -ril sslcommerz` → no results).
  → Phase 4 must build the SSLCOMMERZ adapter/controller from scratch.

## 9. Authentication implementation

New API already provides substantial auth primitives (relevant files exist):

- WebAuthn / Passkeys — `controller/passkey.go`, `model/passkey.go`
- TOTP 2FA — `controller/twofa.go`, `model/twofa.go`, `model/twofa_enrollment.go`
- Login verification / step-up — `controller/login_verification.go`,
  `controller/secure_verification.go`
- Account security / audit — `model/account_security.go`, `controller/audit.go`,
  `model/audit_log.go`
- Sessions — `model/user_session.go` (30 KB), `controller/auth_session.go`
- OAuth/OIDC — `controller/oauth.go`, `controller/custom_oauth.go`
- JWT / access tokens — `controller/access_token.go`, `controller/token.go`
- Authorization — Casbin (`model/casbin_rule.go`, `service/authz/`) plus a
  configurable `authz_roles` table (`model/authz_role.go`).

This means the directive's "reuse New API auth" principle is directly achievable for
Phase 2.

## 10. Billing / pricing primitives present in upstream

- `model/pricing.go`, `model/pricing_default.go`, `model/model_pricing_config.go`
- `model/quota_reserve.go` (pre/post-consumption quota reservation)
- `controller/topup.go`, `model/topup.go` (wallet top-up)
- `model/usedata.go` + flow (usage events)
- `model/subscription.go`, `model/redemption.go` (coupons)

No **double-entry ledger**, no daily-close, no Baidu bill reconciliation, no
SSLCOMMERZ reconciliation exists — these are net-new per the directive.

## 11. Licensing

- License: **AGPLv3** (`LICENSE`).
- `NOTICE` documents **Additional Terms under AGPLv3 Section 7**: modified versions
  must preserve attribution ("Frontend design and development by New API
  contributors."), a visible link to `https://github.com/QuantumNous/new-api`, and
  must not misrepresent origin per Section 7(c).
- `THIRD-PARTY-LICENSES.md` records Apache-2.0 notices (AWS SDK, smithy-go, otp) that
  must be preserved in distributed artifacts.
- `LICENSE-COMPLIANCE.md` ✅ and `PROVIDER_COMMERCIAL_COMPLIANCE.md` ✅ now exist;
  the latter flags Baidu resale authorization as an **unconfirmed launch blocker**.

## 12. Configuration / secret hygiene (findings to fix)

- `.env` in `~/Work/new-api` contains `SESSION_SECRET`, `PORT`, `SQLITE_PATH`,
  `TZ=Asia/Shanghai` — TZ must become `Asia/Dhaka` for the business timezone
  (**reconciled**: both `docker-compose.yml` and `docker-compose.dev.yml` now set
  `TZ=Asia/Dhaka`). The running instance's `.env` still carries the old value until
  it is re-provisioned.
- `.admin-credentials` stores admin username/password in plaintext.
- A file named `"baidu ai cloud"` under `~/Work` contains a **plaintext Baidu API
  credential** (`bce-v3/ALTAK-…/…`). This is outside git but represents a
  secret-handling risk that must be migrated to a secrets manager / env before any
  production work.
- GitHub `GITHUB_TOKEN` is **not set** in this environment (no push/PR automation
  possible right now; Docker, Go 1.27, Node 26, Bun 1.4.2 are available).

## 13. Toolchain available

- Go 1.27.0 (upstream targets 1.25.1), Node 26.8.1, Bun 1.4.2, Docker 29.7.2.
- Build: `make build-web` then `go build`; tests via `make test`
  (root module + `relaykit` module, `GOWORK=off`).

## 14. Summary

The repository is **upstream New API v1.0.0-rc.36 with partial frontend re-branding
and one backend fix (the `baidu_v2` channel name)**, running as an unconfigured
SQLite-backed standalone binary. None of the directive's control-plane, portal,
finance, SSLCOMMERZ, or Baidu-reconciliation components exist yet. Phase 1
scaffolding has not begun. Phase 0 discovery documentation, the initial ADR, and the
12 agent + 10 project-skill definitions are in place.