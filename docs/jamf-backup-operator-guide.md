# Jamf Backup operator guide

## Install

Download the signed `.dmg` from the public GitHub Releases page and drag **Jamf Dossier** to Applications.

## First run

1. Open **Settings** and enter Jamf Pro URL.
2. Save OAuth client credentials (or username/password) to Keychain.
3. Choose a backup destination folder.
4. Review **API Coverage** for the 27 exported object types and manual gaps.

## On-premises database backup

Requires SSH to the Jamf Pro server, Server Tools **2.7.10+**, and optional SSH key in Settings.

## Known release issues

- **`v0.1.1` DMG** — mounts normally but the app **crashes on launch** (release `.app` omitted the SwiftPM resource bundle). Use **`v0.1.2` or newer** from [jamf-dossier Releases](https://github.com/roto31/jamf-dossier/releases).

## Troubleshooting

| Symptom | Action |
|---------|--------|
| App quits immediately after open (old DMG) | Install **v0.1.2+**; do not use `v0.1.1` build |
| HTTP 401 | Grant API role; enable **Stop on first 401** to fail fast |
| TLS errors | Toggle **Verify TLS** off only for lab appliances with self-signed certs |
| Empty summaries | Re-run after upgrade; policy/script XML summaries require valid detail responses |
