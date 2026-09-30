---
name: baidu-qianfan
description: >-
  Integrate Baidu AI Cloud / Qianfan as an upstream provider (V2 API, model mapping, error/usage normalization).
---

# Baidu Qianfan

- Adapter: `relay/channel/baidu_v2/`. Base URL default `https://qianfan.baidubce.com`.
- V2 endpoints: `/v2/chat/completions`, `/v2/embeddings`, `/v2/images/generations`, `/v2/images/edits`, `/v2/rerank`.
- Auth header: `Authorization: Bearer <access_token>`; optional `appid` via `key|appid` split.
- `-search` suffix models enable web_search/citation/trace.
- Do NOT hardcode the model list; sync from official interface (see provider agent).
- Do NOT distribute Baidu credential to customers (§2 credential hierarchy).
