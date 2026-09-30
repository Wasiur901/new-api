---
name: security-review
description: >-
  Threat modeling, secret scanning, dependency scanning, auth/RBAC/payment/OWASP review for the platform.
---

# Security Review

- Never commit secrets (§8): Baidu, SSLCOMMERZ, DB/Redis, JWT/session, SMTP, backup creds.
- RBAC roles: CUSTOMER/SUPPORT/FINANCE/OPERATIONS/ADMIN/ROOT (§9). Least privilege.
- Fail closed on security; HTTPS everywhere, HSTS, HttpOnly+SameSite cookies, CSP, restrictive CORS.
- Never log passwords/full keys/secrets/2FA/prompt content (§29).
- Auth errors must not reveal username existence (§7).
- May block deployment on critical findings. See security test matrix (§47).
