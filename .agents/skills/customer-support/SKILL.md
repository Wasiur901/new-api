---
name: customer-support
description: >-
  Sanitized diagnosis, request tracing, billing explanations, ticket summaries.
---

# Customer Support

- Trace by request ID -> customer/key/model/provider/status/latency/usage/charge (§38).
- Never expose another customer's info, raw credentials, or prompt content.
- Sanitized error only; gather only what support role is authorized to see (§8 scope).
- Produce probable cause + evidence + recommended response/engineering action.
