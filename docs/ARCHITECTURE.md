# ARCHITECTURE — AI API Platform (Target)

> Generated 2026-09-30. This is the *target* architecture per the master directive.
> It extends upstream New API rather than rewriting it (Directive §3).

## 1. Guiding principles

1. **New API is the core gateway** — authentication, API keys, model routing,
   usage/quota, and upstream relay stay in New API. We do not fork-and-rewrite.
2. **Extensions before edits** — adapters, sidecars, and control-plane modules are
   preferred over invasive upstream changes to avoid upgrade/security/licensing
   divergence.
3. **Deterministic financial engine** — charging, crediting, journal posting, and
   payment validation live in reviewed application code, never in an LLM path.
4. **One source of truth** — reuse New API tables where New API already owns the
   concept (users, tokens, channels, models). Add tables only for concepts New API
   does not own (double-entry ledger, SSLCOMMERZ payments, reconciliations).

## 2. Provider-key hierarchy (Directive §2 — invariant)

```
CUSTOMER
    └─ customer-specific New API key      (identifies the customer)
           └─ New API gateway
                 └─ internal channel config
                       └─ Baidu Qianfan credential   (identifies us to Baidu)
                             └─ Baidu AI model
```

Customer keys and upstream credentials are **never** mixed. Every request traces:
`customer_id → api_key_id → request_id → model → provider → channel →
upstream_request_id → usage → provider_cost → customer_charge → ledger`.

Never persist a full customer key or provider secret; store only identifier, prefix,
last-4, hash (or envelope-encrypted reversible value where strictly required).

## 3. Logical topology

```
                         INTERNET
                             │
                    CDN / WAF / DNS
                             │
                        Reverse Proxy (TLS)
                             │
        ┌────────────────────┴───────────────────┐
        │                                        │
   Customer Portal (Next.js)               API Gateway (New API)
        │                                        │
        BFF (TS)                                 │
        │                                        │
  ┌─────┼──────────┐                             │
  │     │          │                             │
Billing Finance  Support                         │
Service Service  Service                         │
  │     │          │                             │
  └─────┼──────────┘                             │
        │                                        │
     PostgreSQL ─── Redis              Provider Layer (Baidu Qianfan)
        │
  Background Workers

Operational: OpenHands · GitHub · CI/CD · Prometheus · Grafana ·
Loki/OTel · Object Storage · Backup Storage · Secrets Manager
```

## 4. Deployment unit (avoid premature microservices)

For the first release, a **modular monolith control-plane** runs alongside New API —
clear module boundaries, co-deployed:

```
/gateway          New API upstream (pinned fork)
/control-plane    billing · finance · payment · provider sync ·
                  reconciliation · support APIs        (Go preferred)
/portal           customer + admin frontends (Next.js/TS, BFF)
/infra            Docker · ingress · monitoring · deployment
/automations      OpenHands automation definitions
/docs             ADRs · runbooks · this doc
/.agents/agents   specialized agent definitions
/.agents/skills   repository skills
```

## 5. Data & persistence

- **PostgreSQL** for the platform (never SQLite in prod); New API supports it natively.
- **Redis** for cache, quota reservation coordination, job coordination.
- **Append-oriented financial records** — no cascade deletes on financial history;
  customer deletion anonymizes PII but preserves legally/accountably required rows.
- **Money** uses exact decimal arithmetic (never binary float); raw units + pricing
  version persisted for reproducibility.
- **Currency** stores `source_currency / source_amount / fx_rate / fx_source /
  fx_timestamp / converted_currency / converted_amount`, never overwriting originals.

## 6. Billing model (Directive §13–23)

- Three distinct prices: **provider list**, **provider effective cost**,
  **customer sell price** — never collapsed.
- Versioned pricing (`model_price_versions`) with `effective_from/to`, audit, hash.
- Wallet = **immutable transaction ledger** (TOPUP / API_USAGE / REFUND / etc.),
  not a single edited balance column.
- Double-entry ledger separate from wallet, with Chart of Accounts, journals,
  trial balance, P&L, balance sheet. Prepaid flow: top-ups credit liability, billed
  only on usage.
- SSLCOMMERZ settled **only** via server-side Order Validation API; idempotent;
  explicit payment state machine.

## 7. Security boundaries (Directive §55)

Browser → BFF → authorized internal interfaces. New API root/admin endpoints are
**never** exposed directly to the public customer frontend. Admin portal is
logically separated (route/subdomain, policy, session controls, IP restrictions).

## 8. Cross-cutting concerns

- **Observability**: structured JSON logs + correlation IDs + OTel + Prometheus +
  Grafana + Loki. Never log secrets or prompt content.
- **Audit**: append-only, with actor/action/target/timestamp/IP/correlation id.
- **Backups**: daily full + WAL/PITR, encrypted dumps, object-storage versioning,
  regular restore drills, documented RPO/RTO.
- **Timezone**: business = `Asia/Dhaka`; provider dates preserved in provider TZ and
  converted explicitly for reports.

## 9. Decision records

Major choices are captured as ADRs under `docs/adr/` (auth integration, portal
architecture, wallet, ledger, provider pricing, payment gateway, secrets, observability,
deployment topology). See IMPLEMENTATION_PLAN.md for sequencing.