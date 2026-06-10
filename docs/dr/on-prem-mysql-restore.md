# On-Prem MySQL Restore

Database restore path for on-premises Jamf Pro using MySQL dumps captured via Server Tools.

## Prerequisites

- Jamf Dossier **Include MySQL backup** tier enabled
- SSH + Server Tools **2.7.10+** on the Jamf Pro server
- MySQL and SSH credentials saved to Keychain in Settings

## Scope

Closest path to full Jamf Pro fidelity for on-prem deployments. Still validate device enrollment and certificate state after restore.

## Related

- [DR Overview](README.md)
- [Operator Guide](../jamf-dossier-operator-guide.md)
- [Device Re-Enrollment](device-re-enrollment.md)
