# PROVIDER_COMMERCIAL_COMPLIANCE.md

Tracks provider commercial authorization required by the master directive (§54)
before any production resale.

> ⚠️ **LAUNCH BLOCKER** — none of the below is confirmed yet. Do not enable
> production billing against a provider until each row is explicitly confirmed
> and the `STATUS` field is `CONFIRMED`.

## 1. Baidu AI Cloud / Qianfan

| Field | Value |
|-------|-------|
| Provider | Baidu AI Cloud / Qianfan |
| Account identifier | _(to be recorded — internal account ID, no secret)_ |
| API used | Qianfan v2 (`qianfan.baidubce.com`) |
| Resale/redistribution rights | **UNCONFIRMED** |
| Geographic restrictions | **UNCONFIRMED** |
| Model-specific restrictions | **UNCONFIRMED** |
| Data-processing restrictions | **UNCONFIRMED** |
| Agreement reference | **UNCONFIRMED** |
| Renewal date | **UNCONFIRMED** |
| Responsible person | **UNASSIGNED** |
| STATUS | ⛔ REQUIRES REVIEW |

### Actions required

1. Obtain the signed Baidu AI Cloud / Qianfan agreement applicable to our account.
2. Confirm the agreement **permits reselling** model access to downstream
   customers (the commercial model described in the master directive §1).
3. Confirm no geographic/model/data-processing restrictions conflict with our
   intended service region (business timezone Asia/Dhaka).
4. Record the responsible person and renewal date.
5. Flip `STATUS` to `CONFIRMED` only after human review evidence is attached.

## 2. SSLCOMMERZ

| Field | Value |
|-------|-------|
| Provider | SSLCOMMERZ (payment gateway) |
| Merchant account | _(to be recorded — store ID reference, no secret)_ |
| Agreement reference | **UNCONFIRMED** |
| Renewal date | **UNCONFIRMED** |
| Responsible person | **UNASSIGNED** |
| STATUS | ⛔ REQUIRES REVIEW |

## 3. Credential hierarchy reminder (not a compliance substitute)

Commercial authorization is separate from our credentialed access. Even with a
working Baidu key (§11), we must not resell until §1 is `CONFIRMED`.

- Never distribute the Baidu upstream credential to customers (§2).
- Customer keys identify the customer; Baidu credential identifies this company
  — never mixed.