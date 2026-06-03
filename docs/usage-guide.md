# Usage Guide

## Quick start

1. Install from [Releases](https://github.com/roto31/jamf-dossier/releases)
2. Configure Jamf URL and API credentials in **Settings**
3. Choose backup folder → **Run Backup**

See [Getting Started](getting-started.md) for details.

## Main window

| Control | Purpose |
|---------|---------|
| **Choose Folder…** | Set backup destination (security-scoped bookmark) |
| **Reveal in Finder** | Open destination after export |
| **Timestamped subfolder** | Each run under `YYYY-MM-DDTHHMMSSZ/` |
| **Backup all** | Export all 41 registry types |
| **Stop on first 401** | Fail fast on first permission error |
| **Run Backup** | Start export after confirmation sheet |

## Settings

| Setting | Effect |
|---------|--------|
| Jamf Pro URL | API base URL |
| OAuth / Basic credentials | Stored in Keychain |
| Verify TLS | Certificate validation |
| Include inventory | Device JSON + FileVault CSV |
| Include MySQL backup | On-prem Server Tools database dump |
| Include Tomcat configuration | SSH copy of Tomcat files |
| SSH host, port, user | On-prem server access |

## End-to-end walkthrough

### Step 1 — Configure

Settings → Jamf URL + credentials → Keychain. See [Setup](setup-installation.md).

### Step 2 — Select scope

- **Backup all** (default) or pick object types in the backup panel
- Optional: **Stop on first 401** for RBAC debugging

### Step 3 — Run backup

Confirm destination and Jamf URL on the confirmation sheet. Progress appears in the UI; detailed log in `logs/export.log`.

### Step 4 — Review output

| Path | Purpose |
|------|---------|
| `documentation/` | Human-readable Markdown |
| `backup/` | Raw JSON/XML |
| `manifest/manifest.json` | Inventory + checksums |
| `manifest/dr-manifest.json` | DR bundle metadata |
| `gaps/manual-workarounds.md` | Gaps and errors |
| `documentation/crosslinks.md` | Policy relationships |

[Output Directory Index](output/index.md)

### Step 5 — Interpret results

| UI result | Meaning |
|-----------|---------|
| Success | All collectors succeeded |
| Completed with errors | Some types failed — review gaps and log |
| Failed to start | Auth or URL problem — check Settings |

## RBAC tuning

Enable **Stop on first 401**, run backup, then check `manifest/run-metadata.json` → `missing_privileges_by_endpoint`. Grant Read in Jamf Pro API role and re-run.

## Re-running backups

Safe to re-run; files are overwritten per object ID. Use timestamped subfolders to keep history.

## Restore planning

Jamf Dossier produces DR bundles for planning. Live restore to another server requires lab procedures — see [DR Overview](dr/README.md). Do not restore to production without testing.

## Related

- [Export Engine](export-engine.md)
- [Operator Guide](jamf-dossier-operator-guide.md)
- [Troubleshooting](troubleshooting.md)
