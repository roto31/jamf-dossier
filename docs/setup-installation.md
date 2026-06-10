# Setup and Installation

Install and configure **Jamf Dossier** on macOS.

## Requirements

| Requirement | Notes |
|-------------|-------|
| macOS | Apple Silicon or Intel |
| Jamf Pro HTTPS access | VPN/firewall must allow API traffic |
| API credentials | OAuth client (preferred) or API user |
| Read privileges | API role with Read on object types you export |

## Install

1. Open [GitHub Releases](https://github.com/roto31/jamf-dossier/releases)
2. Download `Jamf Dossier-<version>-macos.dmg` (**v0.1.2+**; avoid v0.1.1)
3. Drag **Jamf Dossier** to **Applications**
4. Allow Gatekeeper on first launch (Developer ID signed build)

Optional: verify SHA-256 against `release/<version>/checksums.sha256` in this repository.

## Configure

1. Open **Settings**
2. Enter **Jamf Pro URL** (include `:8443` for on-prem if required)
3. Save **OAuth** client ID/secret or **Basic** username/password to Keychain
4. Choose **Verify TLS certificates** (off only for lab self-signed certs)
5. Select backup destination folder on the main screen

Create API credentials in Jamf Pro: **Settings → System Settings → API Roles and Clients**.

## On-premises SSH (optional)

For MySQL/Tomcat/package binaries:

1. **SSH host**, **port**, **username** in Settings
2. Save SSH password or import private key to Keychain
3. Save MySQL password to Keychain when using Server Tools backup

See [Operator Guide](jamf-dossier-operator-guide.md).

## Related

- [Getting Started](getting-started.md)
- [Troubleshooting](troubleshooting.md)
