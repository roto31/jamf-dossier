# Changelog

All notable changes to this project are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]
## [0.1.6] - 2026-06-02

### Changed

- Fix SSH password auth for MySQL/Tomcat backup via SSH_ASKPASS; clearer Jamf connectivity errors when API URL is unreachable.

## [0.1.5] - 2026-06-02

### Changed

- Fix Jamf Pro 11.x API paths for check-in, Self Service, inventory collection, and accounts; add version probe and ordered endpoint fallbacks.

## [0.1.4] - 2026-06-02

### Changed

- Fixed: EndpointRegistry probes Bundle.main before Bundle.module so release .app no longer fatalErrors when the SPM resource bundle is under Contents/Resources (v0.1.3 still crashed on launch).

## [0.1.3] - 2026-06-02

### Changed

- Fixed: copy SwiftPM resource bundle to the .app root so Bundle.module resolves endpoint_registry.json at runtime (supersedes the withdrawn v0.1.2 build, which copied the bundle to Contents/Resources and crashed on launch). Hardened release script with nested codesign of the relocated bundle and added a positive Bundle.module runtime test.


### Added

- GitHub Actions CI (pytest, Swift tests, registry sync check)
- Archive policy and `Archive/removals/` for retired paths
- Export parity fixtures and comparison tooling
- Jamf Backup macOS app (Swift/SwiftUI) and release documentation

## [0.1.2] - 2026-06-01

### Fixed

- **Code:** Release `.app` now embeds `JamfBackup_JamfBackupKit.bundle` so `endpoint_registry.json` loads at launch (fixes crash from `v0.1.1` DMG).
- **Build/CI:** Release workflow fails if `PUBLIC_REPO_TOKEN` is missing on tag builds; CI verifies resource bundle in `.app`.

### Changed

- **Code:** Jamf Dossier branding in release scripts; public repo `roto31/jamf-dossier`.
- **Build/CI:** Optional DMG codesign, notarization, and `hdiutil verify` in release build script.

### Added

- **Code:** `scripts/setup_github_release_secrets.sh` for Apple/GitHub release secrets setup.
- **Code:** `scripts/verify_release_app_bundle.sh` and `ReleaseAppBundleTests` — CI regression for v0.1.1 launch crash (missing resource bundle).
