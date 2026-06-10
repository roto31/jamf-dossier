# Changelog — Jamf Dossier

All notable **Jamf Dossier** (macOS app) release changes are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

Install signed builds from [GitHub Releases](https://github.com/roto31/jamf-dossier/releases). Application source is not published in this repository.

## Documentation

- [Docs index](docs/README.md)
- [Getting Started](docs/getting-started.md)
- [Operator Guide](docs/jamf-dossier-operator-guide.md)
- [Export engine overview](docs/export-engine.md)
- [GitHub Wiki](https://github.com/roto31/jamf-dossier/wiki)

---

## [Unreleased]

---

## [0.5.0] - 2026-06-09

### Added

- App Sandbox enabled for GitHub and Mac App Store distribution (same feature set, single binary).
- In-process SSH/SFTP for on-prem DR tiers (database backup, Tomcat, package binaries) — no dependency on `/usr/bin/ssh`.
- Branded application icon (`AppIcon.icns`).
- Improved accessibility labels on backup flow, wizards, and coverage views.
- Export progress indicator and clearer error alerts for partial failures.

### Changed

- Keychain service unified under app bundle identifier with migration from prior builds.
- SSH host key policy matches OpenSSH `accept-new` (stored in Application Support).

### Fixed

- Settings credential save failures now surface to the user.
- Duplicate Settings menu entry removed.

---

### Added

- Plain-text documentation in exports: script bodies (including base64 fallback), policy scope, smart-group criteria, advanced search criteria.
- Mermaid dependency diagrams for policies and site-wide relationships.
- Expected-unavailable endpoint filtering (JCDS on on-prem, instance 404s) — cleaner `failures.json` and `gaps/skipped-endpoints.json`.
- `dr-manifest.json` on every export (metadata tier at minimum).
- Consumer documentation pages (architecture, troubleshooting, output layout, DR stubs) synced to repo `docs/` and GitHub Wiki.
- Clearer backup tier UI: config/documentation vs full DR bundle.
- FileVault privilege guidance in DR Coverage.

### Fixed

- Backup/documentation index links for GitHub browsing.
- Singleton Jamf settings objects in publish indexes (`jamf_id: singleton`).

---

## [0.3.0] - 2026-06-02

### Added

- Disaster recovery restore workflow (preview and gated live restore) for policies, packages, configuration profiles, and related API objects.
- Inventory collection and package binary fetch (SSH) in full backup mode.
- DR settings UI: backup tier toggles, secrets wizard, restore wizard.
- Expanded endpoint coverage aligned with Jamf Pro 11.x (36 of 41 catalogued object types verified on reference instances).

### Changed

- GitHub releases publish as **prerelease** until explicitly promoted after UAT smoke testing.

---

## [0.2.0] - 2026-06-02

### Added

- **Bundle v2** full-content backup layout: `inventory/`, `binaries/`, encrypted `secrets/` vault, optional `server/` MySQL/Tomcat capture via SSH.
- Restore dry-run engine with `preview.md` and honest `gaps/` reporting.
- Feasibility probe workflow for planning DR scope before large exports.
- Endpoint registry expanded to 41 Jamf object types (inventory, enrollment, patch management, distribution, and related APIs).

---

## [0.1.6] - 2026-06-02

### Changed

- SSH password authentication for on-prem server backup (MySQL/Tomcat) via secure prompt helper.
- Clearer connectivity errors when the Jamf Pro URL is unreachable from your network.

---

## [0.1.5] - 2026-06-02

### Changed

- Jamf Pro **11.x** API path fallbacks for check-in, Self Service, inventory collection, and account settings.
- Export engine parity improvements for 11.x instances.

---

## [0.1.4] - 2026-06-02

### Fixed

- Application launch on macOS when the embedded endpoint registry bundle is installed under `Contents/Resources` (fixes crash seen in 0.1.3 for some installs).

---

## [0.1.3] - 2026-06-02

### Fixed

- Resource bundle load path for `endpoint_registry.json` at launch (supersedes withdrawn 0.1.2 build).
- Release codesign handling for data-only SwiftPM resource bundles.

### Added

- Signed release pipeline with optional notarization and DMG verification.
- Export parity and registry validation in continuous integration.

---

## [0.1.2] - 2026-06-01

### Fixed

- Release `.app` embeds required resource bundle so the app launches from the distributed DMG (fixes 0.1.1 launch crash).

### Changed

- Optional DMG codesign, notarization, and integrity verification in release builds.

### Added

- Release bundle verification tests to prevent missing resource bundle regressions.

---

## [0.1.1] - 2026-06-01

### Added

- **Jamf Dossier** product branding and public release distribution on GitHub Releases.
- Documentation sync to this repository for operators and wiki consumers.

---

## [0.1.0] - 2026-06-01

First public release of **Jamf Dossier** — native macOS backup and documentation client for Jamf Pro.

### Added

- Native macOS app (Swift/SwiftUI) for Jamf Pro configuration export.
- OAuth and Basic API authentication with retry, backoff, and privilege (401) reporting.
- Export layout: `backup/`, `documentation/`, `manifest/`, `logs/`, `gaps/`.
- Per-object markdown documentation with summaries and raw payloads.
- Human-readable indexes per object type with Jamf purpose descriptions.
- Fail-fast `--stop-on-401` mode for API role tuning (CLI/engine).
- Operator documentation (getting started, DR overview, troubleshooting on wiki).

### Changed

- Improved authentication diagnostics for misconfigured URL or credentials.

### Fixed

- macOS 15 compatibility for SwiftUI materials in release builds.

---

## Release tags

| Tag | Summary |
|-----|---------|
| `v0.1.0` | Initial Jamf Dossier release |
| `v0.1.1` | Public branding and docs |
| `v0.1.2` | Resource bundle embed (use 0.1.3+) |
| `v0.1.3` | Launch and codesign fixes |
| `v0.1.4` | Bundle.main registry resolution |
| `v0.1.5` | Jamf Pro 11.x API paths |
| `v0.1.6` | SSH password server backup |
| `v0.2.0` | Bundle v2 DR platform |
| `v0.3.0` | Restore writers and DR UI closeout |
