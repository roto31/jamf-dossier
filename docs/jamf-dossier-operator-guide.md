# Jamf Dossier operator guide

## Install

Download the signed `.dmg` from [GitHub Releases](https://github.com/roto31/jamf-dossier/releases) and drag **Jamf Dossier** to Applications.

## First run

1. Open **Settings** and enter Jamf Pro URL.
2. Save OAuth client credentials (or username/password) to Keychain.
3. Choose a backup destination folder.
4. Review **API Coverage** for the 41 exported object types and manual gaps.
5. Review **DR Coverage** for tier availability on your deployment.

## On-premises database backup

Requires SSH to the Jamf Pro server, Server Tools **2.7.10+**, and SSH credentials in Settings.

1. Enter **SSH host**, **port** (usually 22), and **username** (`jamfadmin`).
2. Click **Save SSH password to Keychain** (or **Import SSH private key**).
3. Click **Save MySQL password to Keychain** (Jamf Pro database user from `DataBase.xml`, usually `jamfsoftware`). The app applies it to Server Tools via `jamf-pro config set` before `database test-connection`.
4. Enable **Include MySQL backup** and/or **Include Tomcat configuration files**.

Tomcat files are copied from `jamf-pro config list` **tomcat-dir** when set, otherwise `/usr/local/jss/tomcat`, `/opt/tomcat`, or `/Library/JSS/Tomcat`.

If SSH works in Terminal but the app failed with `Permission denied (publickey,password)`, ensure the password was saved to Keychain after typing it, or use a key. Clear a bad imported key with **Clear SSH private key**.

## Known release issues

- **`v0.1.1` DMG** — mounts normally but the app **crashes on launch** (release `.app` omitted the SwiftPM resource bundle). Use **`v0.1.2` or newer**.

## Troubleshooting

| Symptom | Action |
|---------|--------|
| **"Internet connection appears to be offline"** on backup | Misleading macOS message: the app could not reach **Jamf Pro HTTPS** (`/api/v1/auth/token`). Fix **Jamf URL** + **API** credentials (OAuth or username/password saved to Keychain). **SSH username/password do not authenticate the API.** |
| App quits immediately after open (old DMG) | Install **v0.1.2+**; do not use `v0.1.1` build |
| HTTP 401 | Grant API role Read privileges; enable **Stop on first 401** to fail fast |
| TLS errors | Toggle **Verify TLS** off only for lab appliances with self-signed certs |
| Empty summaries | Re-run after upgrade; policy/script XML summaries require valid detail responses |

### On-prem connectivity check

From Terminal (replace URL and credentials):

```bash
curl -vk -u 'API_USER:API_PASSWORD' 'https://YOUR_JAMF_HOST/api/v1/jamf-pro-version'
```

If `curl` fails, fix network/VPN/firewall or TLS before using Jamf Dossier. If `curl` works but the app does not, compare the exact URL and toggle **Verify TLS certificates**.

## Related

- [Getting Started](getting-started.md)
- [DR Overview](dr/README.md)
- [Troubleshooting](troubleshooting.md)
