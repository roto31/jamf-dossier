# Device re-enrollment

**Honest scope:** On-prem + MySQL restore is the closest path to full fidelity. Cloud export is configuration + inventory + binaries + vaulted secrets — **not** a literal clone. Existing devices require **re-enrollment** on a new server.

MDM trust is established per device and server. It is not an exportable API object.

After restore to a **new** Jamf Pro server or Cloud tenant:

- Existing devices lose live MDM command trust until re-enrolled.
- On-prem MySQL restore retains inventory rows and escrowed FileVault keys in the database, but APNs/MDM trust still follows Jamf migration constraints.
- API-only Cloud restore keeps inventory JSON for reference; devices must enroll again for management.

Plan communication, PreStage updates, and DEP/ABM sync before cutover.
