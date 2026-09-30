# ADR-0002: Adopt upstream v1.0.0-rc.41 (latest), not pin rc.36

- Status: Accepted (rebase completed 2026-09-30)
- Date: 2026-09-30

## Context

The fork was found pinned at `v1.0.0-rc.36` (commit `ea7cb0b`, shallow/grafted),
4–5 release candidates behind upstream. `origin/main` and tag `v1.0.0-rc.41` both
resolve to the same commit (`2035a82ae`, 2026-09-30).

Local modifications are minimal:
- ~10 lines of frontend rebrand ("LOOP" system name/logo fallbacks, `<title>`).
- 1 line backend fix: `relay/channel/baidu_v2/constants.go` `ChannelName`
  `"volcengine"` → `"baidu_v2"` (copy-paste bug, **still unfixed upstream at rc.41**).

The 141 commits in `rc.36..rc.41` include changes directly required by the master
directive:

- **Security**: admin step-up verification (`6cbb1c7ed`), scoped access tokens
  (`caca52f8d`), safe multi-RP passkey support (`385d2dfd1`).
- **Billing correctness**: expression pricing, pre-consume multiplier
  (`9978ee1e2`), quota reservation, image-quantity validation (`f064bffa2`),
  usage estimation for cut streams.
- **Rate limiting**: reserve model rate-limit slots and judge success by outcome
  (`6237d9d77`).
- **Auth**: reject non-standard roles (`2506e1b98`), restore compatible passkey
  domains (`7fd063819`).

## Decision

Rebase the fork onto the latest upstream (`v1.0.0-rc.41` == `origin/main`) and carry
our two small patches on top, rather than intentionally pinning `rc.36`.

## Alternatives considered

1. **Pin rc.36 and document why.** Rejected: forfeits 141 relevant fixes (security,
   billing, rate limiting) that the directive mandates, with no upside beyond
   avoiding a small rebase.
2. **Track upstream `main` continuously.** Postponed: acceptable later as an
   automation (AUTOMATION-4), but for now we pin a known release candidate tag.

## Consequences

- Must unshallow the clone and perform a rebase; expect conflicts limited to
  `web/src/styles/theme.css` and the files upstream "refreshed" (logo/site settings).
- The `baidu_v2` `ChannelName` fix and the rebrand must be re-applied/verified after
  rebase since they are not upstream.
- Re-review the AGPL `NOTICE` terms after any merge to ensure attribution survives.

## Migration implications

- Rebuild `web/dist` and the backend binary after rebase (AUTOMATION-3/§49 immutable
  builds).
- Re-run the full test suite (root + relaykit) before any Phase 1 work.