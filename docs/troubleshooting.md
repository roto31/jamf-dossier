# Troubleshooting

Verified against Jamf Dossier release behavior and backup output layout.

## Authentication failures

### Symptom

Backup fails immediately; `logs/export.log` shows authentication error; few or no objects collected.

### Fixes

| Cause | Fix |
|-------|-----|
| Wrong Jamf URL | Use base URL with port for on-prem (`:8443`) |
| Missing credentials | Save OAuth or Basic credentials in Settings → Keychain |
| Invalid OAuth secret | Regenerate in Jamf Pro → API Roles and Clients |
| **Offline** error message | Usually unreachable Jamf HTTPS — not macOS internet; fix URL/VPN/firewall/TLS |

```bash
curl -vk -u 'USER:PASS' 'https://YOUR_HOST/api/v1/jamf-pro-version'
```

## SSL certificate errors

Toggle **Verify TLS certificates** off in Settings only for lab appliances with self-signed certs.

## HTTP 401 on specific types

Some types export; others fail.

1. Jamf Pro → API role → grant **Read** for failing resource
2. Enable **Stop on first 401** to find first failure
3. Check `manifest/run-metadata.json` → `missing_privileges_by_endpoint`

## App crashes on launch

Install **v0.1.2 or newer**. `v0.1.1` DMG is broken (missing resource bundle).

## Empty object type

Jamf may have zero objects of that type, or list request failed — check `logs/export.log` and `logs/failures.json`.

## On-prem package binaries missing

Requires SSH credentials in Settings. Without SSH, on-prem deployments write `gaps/package-binaries-ssh-required.md`.

## Inventory / FileVault gaps

Grant **Computers: Read** and **View Disk Encryption Recovery Key** on the API role. See `gaps/computers-inventory-privilege-missing.md` in backup folder.

## SSH works in Terminal but not in app

Re-save SSH password to Keychain after entering it, or import a valid private key. Clear bad keys with **Clear SSH private key**.

## MySQL backup fails

- Server Tools 2.7.10+ required on Jamf server
- Save MySQL password to Keychain (user from `DataBase.xml`, usually `jamfsoftware`)
- Confirm `jamf-pro database test-connection` works via SSH manually

## Partial backup is valid

RBAC gaps or **Stop on first 401** may leave a partial but useful backup. Review `gaps/manual-workarounds.md` before treating export as complete.

## Getting help

1. `logs/export.log` in backup folder
2. `logs/failures.json`
3. `gaps/manual-workarounds.md`
4. [GitHub Issues](https://github.com/roto31/jamf-dossier/issues)
