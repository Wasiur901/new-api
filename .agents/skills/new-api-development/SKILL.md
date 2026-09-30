---
name: new-api-development
description: >-
  Develop and maintain the New API gateway (QuantumNous/new-api) and its control-plane integration.
---

# New API Development

Core gateway is upstream `QuantumNous/new-api` (AGPL-3.0). We extend, not rewrite (see docs/adr/0001).

- Gateway code lives at repo root (Go). Web frontend in `web/` (React + Vite + Tailwind v4).
- Provider adapters: `relay/channel/<provider>/`. Baidu V2: `relay/channel/baidu_v2/`.
- Channel types: `constant/channel.go` (`ChannelTypeBaiduV2 = 46`).
- Build: `go build` (binary `new-api`). Frontend: `cd web && bun install && bun run build` -> `web/dist` (embedded via Go `embed`).
- Run: `./new-api --port 3000 --log-dir logs`.
- Never ship mental-model-breaking changes to upstream core without an ADR.
- Keep upstream fork clean for rebase (Automation #4).
