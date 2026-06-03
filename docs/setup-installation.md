# Setup & Installation

## System requirements

| Requirement | Notes |
|-------------|-------|
| macOS | 15 or later |
| Disk space | Depends on Jamf environment size; package binaries can be large |
| Network | HTTPS to Jamf Pro (and SSH for on-prem DR tiers) |
| Jamf credentials | OAuth API client (preferred) or API user with Read privileges |

## Install Jamf Dossier

1. Download `Jamf Dossier-<version>-macos.dmg` from [Releases](https://github.com/roto31/jamf-dossier/releases)
2. Verify SHA-256 (optional) using `release/<version>/checksums.sha256`
3. Open DMG → drag to **Applications**
4. Launch **Jamf Dossier**

Do **not** install `v0.1.1` — use v0.1.2 or newer.

## Jamf Pro API setup

1. Jamf Pro → **Settings → System Settings → API Roles and Clients**
2. Create an API client (OAuth) or API user
3. Assign a role with **Read** for each object class you need
4. In Jamf Dossier **Settings**, save credentials to Keychain

### URL format

| Deployment | Example |
|------------|---------|
| Jamf Cloud | `https://yourtenant.jamfcloud.com` |
| On-prem | `https://jamf.example.com:8443` |

Use the base URL only — no `/api` or `/JSSResource` suffix.

### TLS

| Setting | When |
|---------|------|
| **Verify TLS** on | Production and Jamf Cloud |
| **Verify TLS** off | Lab only — self-signed on-prem certificates |

## On-prem SSH (optional)

For MySQL backup, Tomcat configs, and package binary SCP:

| Field | Typical value |
|-------|---------------|
| SSH host | Jamf Pro server hostname |
| SSH port | `22` |
| SSH user | `jamfadmin` |
| MySQL user | `jamfsoftware` (from `DataBase.xml`) |

Save SSH and MySQL passwords to Keychain in Settings, or import an SSH private key.

Requires **Jamf Pro Server Tools 2.7.10+** on the server for database backup.

## First backup

See [Getting Started](getting-started.md).

## Updates

Download a newer DMG from Releases and replace the app in Applications. Your Keychain credentials and destination folder bookmark persist.

## Related

- [Operator Guide](jamf-dossier-operator-guide.md)
- [Troubleshooting](troubleshooting.md)
