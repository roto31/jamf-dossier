# Project status

**Last updated:** 2026-06-02  
**Primary branch:** `main` @ `49d739f` (v0.1.2 release fix + launch regression test)  
**Private Release CI (v0.1.2):** https://github.com/roto31/Jamf-Settings-Analysis/actions/runs/26797022190 — build/sign/notarize/DMG **green**; public publish **failed** (no `PUBLIC_REPO_TOKEN`)  
**Public docs/releases:** [roto31/jamf-dossier](https://github.com/roto31/jamf-dossier) — **Jamf Dossier**

## Overall

| Lens | Score | Notes |
|------|-------|-------|
| Engineering / CI / parity | **~95%** | CI, fixtures, live workflow, messaging checks |
| Consumer release (signed DMG on public Releases) | **~95%** | Pipeline + `v0.1.0`/`v0.1.1` private CI green; **`v0.1.2`** ships launchable app after operator tag + `PUBLIC_REPO_TOKEN` |
| Full Jamf GUI/API coverage | **N/A (by design)** | **27** cataloged API types + manual gaps |

## Completed

| Item | Status |
|------|--------|
| Release build/sign/notarize/DMG script | Done — [`scripts/release_build_macos_app.sh`](../scripts/release_build_macos_app.sh) |
| SwiftPM resource bundle in release `.app` | Done — `JamfBackup_JamfBackupKit.bundle` under **`Contents/Resources`** (codesign-safe); `EndpointRegistry.load()` falls back to `Bundle.main` subdirectory lookup because SPM `Bundle.module` expects the bundle beside the `.app` root. v0.1.3 supersedes withdrawn v0.1.2 (which used `Bundle.module` only and crashed on launch). |
| Launch crash regression test | Done — [`scripts/verify_release_app_bundle.sh`](../scripts/verify_release_app_bundle.sh) + `ReleaseAppBundleTests.swift` in CI/release |
| Tag `v0.1.2` pushed | Done — private artifact `jamf-dossier-macos-v0.1.2` (DMG in Actions artifacts) |
| [`release.yml`](../.github/workflows/release.yml) artifacts + resource verify + publish gate | Done |
| Public publish script | Done — [`scripts/publish_public_release.sh`](../scripts/publish_public_release.sh) |
| Jamf Dossier branding + [`release_product.env`](../scripts/release_product.env) | Done |
| [`setup_github_release_secrets.sh`](../scripts/setup_github_release_secrets.sh) | Done |
| PR #4 — remove tracked `JamfBackup/.build` | Done (archived) |
| Private releases `v0.1.0`, `v0.1.1` | Done — Release workflow green (sign + notarize) |
| Public repo | Done — [`jamf-dossier`](https://github.com/roto31/jamf-dossier) (not `Jamf-Backup`) |
| Public `v0.1.1` (manual) | Done — CI skipped publish (no `PUBLIC_REPO_TOKEN`); **DMG mounts but app crashed on launch** (missing resource bundle) — use **`v0.1.2+`** |
| Apple release secrets on private repo | Done — `BUILD_CERTIFICATE_*`, `ASC_*`, `NOTARY_TEAM_ID`, etc. |
| Live parity hardening | Done — [`parity-live.yml`](../.github/workflows/parity-live.yml) |
| Pre-backup confirm sheet | Done — [`ConfirmBackupSheet.swift`](../JamfBackup/Sources/JamfBackupApp/Views/ConfirmBackupSheet.swift) |
| API coverage messaging guard | Done — [`scripts/verify_api_coverage_messaging.sh`](../scripts/verify_api_coverage_messaging.sh) |

## Operator checklist (“shipped”)

| Step | Status | Notes |
|------|--------|-------|
| Apple + release secrets | **Done** | [`docs/release/apple-developer-github-actions-setup.md`](release/apple-developer-github-actions-setup.md); validated via [`setup_github_release_secrets.sh`](../scripts/setup_github_release_secrets.sh) |
| Public docs repo `jamf-dossier` | **Done** | [`docs/release/public-repo-setup.md`](release/public-repo-setup.md) |
| `PUBLIC_REPO_TOKEN` on private repo | **Remaining** | Fine-grained PAT, Contents R/W on `roto31/jamf-dossier` only — required for automated public publish |
| `JAMF_URL`, `JAMF_CLIENT_ID`, `JAMF_CLIENT_SECRET` | **Remaining** | For [Live export parity](../.github/workflows/parity-live.yml) |
| Tag **`v0.1.2`** + verify CI + public DMG | **Partial** | Tag pushed; [Release run](https://github.com/roto31/Jamf-Settings-Analysis/actions/runs/26797022190) built `Jamf Dossier-0.1.2-macos.dmg` — **re-run publish** after `PUBLIC_REPO_TOKEN` (or `gh workflow run` / re-tag) for [public release](https://github.com/roto31/jamf-dossier/releases) |
| Local launch test from DMG | **Remaining** | App stays running; API Coverage / backup UI loads |
| Live export parity (one run) | **Remaining** | Actions → **Live export parity** → Run workflow; paste run URL below |

**Parity run URL:** _(pending operator run)_

## API scope (unchanged)

Exports **27** Jamf API object types per [`jamf_exporter/endpoint_registry.py`](../jamf_exporter/endpoint_registry.py). Not every Settings UI field. See [endpoint-verification.md](endpoint-verification.md) and in-app **API Coverage**.
