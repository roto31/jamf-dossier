# Device Re-Enrollment

After cloud API restore or new server stand-up, managed devices do **not** automatically reconnect.

## Expectations

| Scenario | Device behavior |
|----------|-----------------|
| Cloud config restore to new tenant | Re-enroll all devices |
| On-prem MySQL restore | Validate enrollment profiles; re-enroll as needed |
| Config-only backup | Plan enrollment before cutover |

## Operator checklist

1. Export current enrollment profiles and MDM payloads from backup
2. Stand up replacement Jamf Pro instance
3. Restore configuration (API or MySQL path)
4. Re-deploy enrollment methods (ADE, user-initiated, etc.)
5. Validate check-in and policy execution on pilot devices

## Related

- [DR Overview](README.md)
- [Cloud API Restore](cloud-api-restore.md)
