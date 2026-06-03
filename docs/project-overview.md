# Project Overview

## Purpose

**Jamf Dossier** is a macOS application that connects to Jamf Pro, exports configuration objects via the Jamf Pro API and Classic API, and produces:

1. **Structured documentation** — Markdown with extracted summaries and raw payloads
2. **Structured backups** — JSON or XML for audit, diff, and disaster-recovery planning
3. **Operational metadata** — manifests with SHA-256 checksums, cross-link reports, gap analysis, DR bundle metadata

## Problem it solves

Jamf Pro environments contain hundreds of interlinked objects. Manual documentation is error-prone and quickly stale. Jamf Dossier automates:

- Export of **41** API object types
- Per-object checksums and inventory manifests
- Cross-reference mapping (policies ↔ scripts/packages)
- Identification of settings that require manual UI capture

## Tech stack (shipped product)

| Component | Technology |
|-----------|------------|
| Application | Native Swift / SwiftUI (macOS 15+) |
| HTTP | URLSession with Jamf Pro API |
| Credentials | macOS Keychain |
| Endpoint catalog | `endpoint_registry.json` in app bundle |
| Output formats | JSON, XML, Markdown, CSV |
| On-prem DR | SSH + Jamf Pro Server Tools |

Application source is proprietary. This repository publishes **documentation and release artifacts only**.

## Capabilities

| Feature | Description |
|---------|-------------|
| Metadata export | Policies, groups, profiles, scripts, packages, LDAP, webhooks, prestages, etc. |
| DR Bundle v2.0 | Standardized folder layout with `dr-manifest.json` |
| Device inventory | Computers and mobile devices via Jamf Pro API |
| Package binaries | Cloud JCDS or on-prem SCP from JSS cache |
| On-prem server backup | MySQL via Server Tools, Tomcat configuration via SSH |
| API coverage UI | In-app list of 41 endpoints and 2 manual gaps |
| Restore preview | Dry-run restore planning (lab use; see DR docs) |

## Supported object types

41 endpoint specs in the bundled registry. Categories include policies, scripts, packages, groups, profiles, prestages, settings, org structure, accounts, distribution points, inventory list endpoints, VPP, SSO settings, and JCDS (cloud).

Full list: open **API Coverage** in the app or see [API Endpoint Citations](api-endpoint-citations.md).

## Known API gaps

| Object type | Limitation |
|-------------|------------|
| `sso_configuration` | No stable public API for complete SSO provider config |
| `api_integrations_secrets` | Shared secrets not retrievable in clear text |

Documented in backup `gaps/manual-workarounds.md` and [manual-sso.md](manual-sso.md).

## Version

Current release version is in [CHANGELOG.md](../CHANGELOG.md) and GitHub Releases. Each backup records `exporter_version` in `manifest/run-metadata.json`.
