---
name: deployment
description: >-
  Docker, ingress/reverse proxy, TLS, monitoring, backups, and release procedures.
---

# Deployment

- Docker Compose for dev; production must be K8s-migratable (§5).
- PostgreSQL only in production (never SQLite). Redis for cache/coordination.
- Reverse proxy: Nginx/Caddy/Traefik + automated TLS (TLS 1.2 min, 1.3 preferred).
- Immutable versioned images; DB migration gate; health checks; rollback (§49).
- Encrypted backups daily + PITR; test restores (§31). Never deploy unreviewed security-sensitive changes.
