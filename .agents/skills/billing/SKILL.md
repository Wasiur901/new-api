---
name: billing
description: >-
  Usage costing, pricing versions, provider cost, customer charge, quota and reconciliation.
---

# Billing

- Three prices are distinct: provider list, provider effective cost, customer sell (§13).
- Versioned pricing: never overwrite historical; every request references its pricing version.
- Money math: exact decimal arithmetic (never binary float). Persist raw units + pricing version.
- Cost formula (§17): input*in_rate + output*out_rate + cache + other.
- Currency: preserve source_currency + fx_rate + timestamps (§18). BDT customer / CNY provider.
- Provider-reported usage is authoritative; mark estimates clearly.
- Billing test matrix (§45) is a release blocker; never silently modify financial records.
