# Device re-enrollment

MDM trust is established per device and server. It is not an exportable API object.

After restore to a **new** Jamf Pro server or Cloud tenant:

- Existing devices lose live MDM command trust until re-enrolled.
- On-prem MySQL restore retains inventory rows and escrowed FileVault keys in the database, but APNs/MDM trust still follows Jamf migration constraints.
- API-only Cloud restore keeps inventory JSON for reference; devices must enroll again for management.

Plan communication, PreStage updates, and DEP/ABM sync before cutover.
