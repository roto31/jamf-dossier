# Jamf Dossier

**Jamf Dossier** is a macOS application for full Jamf Pro environment documentation and structured configuration backup. It exports policies, groups, profiles, scripts, packages, prestages, LDAP, webhooks, and 40+ other object types — producing human-readable Markdown, raw JSON/XML backups, DR bundle manifests, checksums, cross-link reports, and gap analysis.

## Download

Install from [GitHub Releases](https://github.com/roto31/jamf-dossier/releases):

1. Download `Jamf Dossier-<version>-macos.dmg`
2. Open the DMG and drag **Jamf Dossier** to Applications
3. Launch from Applications (macOS 15+)

Verify the SHA-256 checksum in `release/<version>/checksums.sha256` before installing.

> **Do not use `v0.1.1`** — that DMG crashes on launch. Use **v0.1.2 or newer**.

## Quick start

1. Open **Jamf Dossier** → **Settings**
2. Enter your Jamf Pro URL and save API credentials to Keychain (OAuth client or username/password)
3. Choose a backup destination folder
4. Click **Run Backup**

See the [Getting Started](https://github.com/roto31/jamf-dossier/wiki/Getting-Started) wiki page for the full walkthrough.

## Documentation

| Resource | Link |
|----------|------|
| **GitHub Wiki** | [github.com/roto31/jamf-dossier/wiki](https://github.com/roto31/jamf-dossier/wiki) |
| **Docs folder** | [docs/README.md](docs/README.md) |
| **Operator guide** | [docs/jamf-dossier-operator-guide.md](docs/jamf-dossier-operator-guide.md) |
| **DR overview** | [docs/dr/README.md](docs/dr/README.md) |
| **Changelog** | [CHANGELOG.md](CHANGELOG.md) |

## What gets backed up

Each run writes a **DR Bundle v2.0** under your chosen folder:

```
backup-run/
├── backup/              # Raw JSON/XML per object
├── documentation/       # Markdown docs + crosslinks
├── manifest/            # manifest.json, dr-manifest.json, run metadata
├── gaps/                # API gaps and privilege notes
├── logs/                # export.log, failures.json
├── inventory/           # Device inventory (when enabled)
├── binaries/            # Package .pkg files (when enabled)
└── server/              # On-prem Tomcat/MySQL artifacts (when enabled)
```

See [Output Directory](https://github.com/roto31/jamf-dossier/wiki/Output-Directory) for complete artifact documentation.

## Requirements

| Requirement | Notes |
|-------------|-------|
| macOS | 15 or later |
| Jamf Pro | Cloud or on-premises with HTTPS API access |
| API credentials | OAuth client (preferred) or API user with Read privileges |
| On-prem DR extras | SSH to Jamf server for package binaries and MySQL/Tomcat backup |

## Authentication

Jamf Dossier uses the same Jamf Pro API auth as the Jamf API:

1. OAuth2 client credentials (`POST /api/v1/oauth/token`) — preferred
2. Basic-to-token fallback (`POST /api/v1/auth/token`)

Credentials are stored in the macOS Keychain, not in backup files.

## Proprietary software

Application source code is **not** published in this repository. This repo contains user documentation, release metadata, and signed release artifacts only.

See [docs/legal/proprietary-notice.md](docs/legal/proprietary-notice.md).

## API references

- [Jamf Pro API Overview](https://developer.jamf.com/jamf-pro/docs/jamf-pro-api-overview)
- [Classic API Overview](https://developer.jamf.com/jamf-pro/docs/getting-started-2)
- [Endpoint citations](docs/api-endpoint-citations.md)

## Support

1. [Troubleshooting wiki](https://github.com/roto31/jamf-dossier/wiki/Troubleshooting)
2. Review `gaps/manual-workarounds.md` in your backup folder
3. Open a GitHub issue on this repository
