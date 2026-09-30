---
name: sslcommerz
description: >-
  Integrate the SSLCOMMERZ payment gateway (hosted flow, IPN validation, idempotent wallet credit).
---

# SSLCOMMERZ

- Hosted payment flow preferred (§19). Never credit on browser success_url alone.
- Validate every IPN server-to-server via the SSLCOMMERZ Order Validation API.
- Idempotency: same `tran_id` must never credit wallet twice (unique constraint + idempotency key).
- States: CREATED->SESSION_CREATED->PENDING->VALIDATING->PAID->VALIDATED (also FAILED/CANCELLED/EXPIRED/REFUND_*).
- Secrets: `SSLCOMMERZ_STORE_ID`, `SSLCOMMERZ_STORE_PASSWORD` — never commit. Sandbox first.
- Test matrix in §46 is a release blocker.
