# Cloud API Restore

API-based restore path for Jamf Cloud tenants using exported configuration objects.

## Scope

- Restores **configuration objects** exported to `backup/` (policies, scripts, profiles, etc.)
- Does **not** restore managed device state or enrollment
- Devices require [re-enrollment](device-re-enrollment.md) after standing up a new tenant

## Planning

1. Complete a full config backup with Jamf Dossier
2. Review `manifest/dr-manifest.json` and `gaps/manual-workarounds.md`
3. Run restore preview in a lab tenant before production cutover

## Related

- [DR Overview](README.md)
- [Device Re-Enrollment](device-re-enrollment.md)
