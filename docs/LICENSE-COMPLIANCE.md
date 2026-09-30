# LICENSE-COMPLIANCE.md

> This document records the applicable upstream license terms and the
> attribution/obligation tracking required by the master directive (§53).
> It is **not** legal advice; final legal review is a launch gate.

## 1. Upstream project

| Field | Value |
|-------|-------|
| Project | New API |
| Source | https://github.com/QuantumNous/new-api |
| Module | `github.com/QuantumNous/new-api` |
| License | **GNU Affero General Public License v3.0 (AGPL-3.0)** |
| Pinned version (this repo) | `v1.0.0-rc.36` |
| Latest upstream (at last check) | `v1.0.0-rc.40` |
| License file | `LICENSE` (verbatim AGPL-3.0 text) |
| Attribution file | `NOTICE` |

## 2. Additional Terms (AGPLv3 §7) — must preserve

`NOTICE` declares additional permissions/obligations under **AGPLv3 §7(b)**:

> "Frontend design and development by New API contributors."

and, for modified versions presenting a user interface, a **visible link** to
`https://github.com/QuantumNous/new-api` in a prominent about/legal/footer/
attribution location.

`NOTICE` also invokes **§7(c)**: modified versions must **mark their changes** and
must **not misrepresent origin**.

### Compliance actions

- [x] Keep `NOTICE` intact and shipped with all distributed artifacts
      (Docker image, standalone binary, frontend bundle, Electron installer).
- [x] Keep `LICENSE` intact.
- [x] Keep `THIRD-PARTY-LICENSES.md` intact and shipped.
- [ ] Preserve the attribution string and visible upstream link in the branded UI
      (do **not** strip attribution during re-branding).
- [ ] Mark modified files/features clearly (see §4 "Modifications log").

## 3. AGPL implications (what we must understand, not conclude)

AGPL-3.0 is a network-copyleft license. Providing the software over a network
(including our SaaS API platform) triggers the source-offer obligations. Key
obligations we must be prepared to satisfy:

1. **Source availability** — users interacting with our network service must be
   able to obtain the Corresponding Source (including our modifications).
2. **Modified-source marking** — changes must be dated and described.
3. **Preserve notices** — copyright, license, and attribution notices must remain.

> ⚠️ **Launch blocker for legal review**: before commercial launch, obtain
> human/legal confirmation of our AGPL obligations and the chosen compliance
> strategy. If the business wishes to avoid AGPL network-copyleft obligations,
> evaluate obtaining a commercial license from QuantumNous (the project owner).
> We do not conclude legal questions here.

## 4. Modifications log

| Date | Area | Description |
|------|------|-------------|
| (to date) | `web/` | Frontend re-branding: logos, fonts (Gabarito/Bungee), theme colors, removal of Docs nav module. Uncommitted until finalized. |
| (to date) | `relay/channel/baidu_v2/constants.go` | Fix `ChannelName` `"volcengine"` → `"baidu_v2"` (bug in upstream copy). |
| (to date) | `docs/` | Added Phase 0 discovery/plan/compliance docs (new files, additive). |

## 5. Third-party dependencies

- Direct-dependency Apache-2.0 NOTICE entries reproduced in `NOTICE`
  (AWS SDK for Go, smithy-go, otp) must be preserved.
- Full dependency list: `go.mod` / `go.sum` (Go), `web/package.json` /
  `web/bun.lock` (frontend), `THIRD-PARTY-LICENSES.md` (upstream-maintained).

## 6. Responsibilities

- **Owner**: engineering lead (to be assigned).
- **Review rhythm**: re-check on every upstream upgrade (Automation #4) and
  before each release.