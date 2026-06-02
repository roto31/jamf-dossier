# Lotus Home Academy — Site Overview

This wiki documents the Jamf Pro configuration backup for **Lotus Home Academy**.

## Jamf Server

| Property | Value |
|----------|-------|
| Site name | Lotus Home Academy |
| Jamf Pro URL | `https://lotushomeacademy.edu:8443` |
| Deployment | On-premises (port 8443) |
| Backup folder | [`Lotus-Home-Academy-Backup/`](/Lotus-Home-Academy-Backup/README.md) |

## What This Backup Contains

The backup captures Jamf Pro management objects that define how Macs and mobile devices at Lotus Home Academy are configured, grouped, and automated.

| Object Type | Count | Role on Jamf Pro |
|-------------|-------|------------------|
| [computer_configuration_profiles](object-types/computer-configuration-profiles.md) | 9 | Configuration profiles deliver MDM settings to Macs—Wi-Fi, restrictions, font management, browser policies, caching, and other system/user payloads. |
| [computer_groups](object-types/computer-groups.md) | 21 | Computer groups organize Macs for scoping policies, configuration profiles, and App Store apps. Smart groups use dynamic criteria; static groups use fixed membership. |
| [mac_app_store_apps](object-types/mac-app-store-apps.md) | 7 | Mac App Store app records define App Store titles Jamf can license and deploy to scoped Macs via policies or Self Service. |
| [mobile_device_groups](object-types/mobile-device-groups.md) | 3 | Mobile device groups organize iPads and iPhones for scoping mobile configuration profiles, apps, and policies. |
| [packages](object-types/packages.md) | 24 | Packages are deployable installers (typically PKG) cached on Jamf Pro distribution points and installed on Macs when referenced by policies. |
| [policies](object-types/policies.md) | 72 | Policies are the primary automation engine on Jamf Pro. They run scripts, deploy packages, enforce configuration, update inventory, and trigger OS upgrades on scoped Macs when their triggers fire (check-in, enrollment, schedule, etc.). |
| [scripts](object-types/scripts.md) | 42 | Scripts are reusable command payloads stored in Jamf Pro. Policies and other workflows reference them to execute shell commands on managed Macs at runtime. |

## Typical Workflows on This Server

Based on exported policies and scripts, this Jamf instance primarily:

1. **Onboards new Macs** via Setup Your Mac policies and SwiftDialog scripts
2. **Deploys software** through Installomater-driven policies and cached packages
3. **Manages OS upgrades** with Erase-Install packages (Monterey through Tahoe)
4. **Scopes configuration** via computer groups (smart groups for OS/app state)
5. **Enforces profiles** for Wi-Fi, Chrome, fonts, login window, and content caching
6. **Maintains inventory** with daily Update Inventory policy

These observations are inferred from exported object names and payloads in this snapshot.

## Related

- [Snapshot Metadata](snapshot-metadata.md)
- [Jamf Object Architecture](jamf-object-architecture.md)
- [Project Usage Guide](../usage-guide.md)
