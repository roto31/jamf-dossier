# Changes Log

Operational record of completed tasks in this repository.

## 2026-10-14 — Add MIT license and third-party notices

**What was done:** Added the MIT License and third-party attribution for release builds. Third-party licenses for the v0.5.3 build were verified against each upstream repository at the commit pinned in `Package.resolved`, and their texts were copied verbatim into `third_party_licenses/`. The former legal notice page was renamed to a license notice and rewritten for MIT, and README/CHANGELOG/doc wording was updated to match. No application code, binaries, or release artifacts were changed.

**Files changed:** `LICENSE`, `THIRD_PARTY_NOTICES.md`, `third_party_licenses/*` (17 files), `docs/legal/license-notice.md` (renamed from the former legal notice page), `README.md`, `CHANGELOG.md`, `docs/README.md`, `docs/export-engine.md`, `docs/CHANGES.md`

**Commands run:** `git switch -c chore/add-license-and-notices`; `gh api` reads of upstream license files at pinned tags; `git mv` of the former legal notice page to `docs/legal/license-notice.md`; `git commit` (two commits: initial license work, then wording follow-up)

**Verified by:** tag-to-revision match for every pinned package; symbol inspection of the v0.5.3 executable; link check for the renamed notice; staged diff limited to license, notice, and documentation files

**Rollback:** `git switch main && git branch -D chore/add-license-and-notices`
