# ADR-0001: Extend New API, do not fork-rewrite

- **Status**: Accepted
- **Date**: 2026-09-30
- **Deciders**: Engineering (OpenHands) — pending human confirmation
- **Scope**: Repository strategy for the AI API platform

## Context

We are building a commercial multi-tenant AI API platform (Baidu Qianfan
upstream, SSLCOMMERZ payments, BDT billing) on top of the upstream project
`QuantumNous/new-api`. Upstream already provides the OpenAI-compatible gateway,
model/channel management, quota engine, user/auth, and token/key management —
the core of what we need.

There is a strong temptation, when building a custom branded product, to rewrite
or deeply modify upstream to force our branding and business rules into its core.

## Decision

Treat New API as the core gateway and model-management engine. Implement all
platform-specific behavior (custom portal, billing, finance ledger, payments,
reconciliation, support) as **adapters, sidecars, and control-plane modules** —
not invasive changes to upstream core.

Before changing New API core code, ask: can this be done via an adapter,
service, plugin, or API integration? If yes, prefer the external implementation.

## Alternatives considered

1. **Fork-and-rewrite** — accept upstream, then extensively modify/inline our
   branding and business logic into core. Rejected: upgrade conflicts, security
   regressions, maintenance burden, divergence, licensing/compliance risk.
2. **Full upstream + external control plane (chosen)** — keep a clean upstream
   fork; build `/control-plane`, `/portal`, `/infra`, `/automations` around it.
3. **Fork + minimal upstream fixes only** — a narrower variant of (2); we accept
   this for occasional upstream bugs (e.g. the `baidu_v2` ChannelName fix) but
   otherwise stay external.

## Consequences

- **Positive**: upstream security patches and features track cleanly; low
  maintenance; clear dependency boundaries (mandatory per §4).
- **Negative**: must maintain clean-fork hygiene (regularly rebase/lift upstream,
  see Automation #4); some branding must be injected via config/adapters rather
  than direct edits.
- **Migration**: none — greenfield strategy decision.

## Compliance

Frontend re-branding is a *modification* of AGPL-3.0 upstream; see
`LICENSE-COMPLIANCE.md` for notice/attribution obligations. The visible upstream
attribution link (§7(b)) must remain intact despite re-branding.