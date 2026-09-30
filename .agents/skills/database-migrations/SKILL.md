---
name: database-migrations
description: >-
  Schema quality, indexes, migration safety, query performance, backups and data integrity.
---

# Database Migrations

- FK + unique + check constraints, proper indexes, timestamps, migration files (§43).
- Financial records append-oriented; never cascade-delete financial history.
- Every migration: upgrade procedure + rollback + production impact analysis.
- Reuse New API tables where upstream owns the concept; avoid duplicate sources of truth.
- Destructive migrations require human approval (§62).
