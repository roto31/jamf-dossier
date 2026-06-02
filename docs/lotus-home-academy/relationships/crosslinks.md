# Policy Crosslinks

Maps which scripts and packages are referenced by which policies on the Lotus Home Academy Jamf server.

Full report: [`crosslinks.md`](/Lotus-Home-Academy-Backup/documentation/crosslinks.md)

# Object Crosslinks

## Scripts -> Policies

- `00_Prepare_SwiftDialog.sh`: Atom, Brave, Chrome, Firefox, Flux, HMA VPN, Handbrake, MS Edge, Silhouette Studio, Tailscale, VLC, VMWare Fusion, Virtual Box, Visual Studio Code, Zoom
- `App-Auto-Patch`: App Update - App Auto-Patch
- `Auto Set Computer Name v. 3`: Auto Name Computers, Name Computer
- `Cache macOS`: Cache macOS Monterey, Cache macOS Ventura
- `Edit Hosts File - Wireless IP`: Edit Hosts File for Wireless
- `Installomater v 10.1 machine setup`: Chrome Setup, Firefox Setup, MS Edge Setup, Zoom Setup
- `Installomater v 10.5`: Asana Setup, Atom, Brave, Brave Setup, Chrome, Chrome Set-up, Firefox, Flux, HMA VPN, Handbrake, MS Edge, Microsoft Visual Studio Code, Silhouette Studio, Slack Setup, Tailscale, Tailscale Setup, VLC, Visual Studio Code, Zoom
- `Installomater v. 10.8`: Virtual Box
- `Installomater v. 9`: App Update - Brave Browser, Asana, Canva, MS Office Install, MS Office Install, MS Office Install, MS Office Install setup, MS Office Install setup, MS Office Install setup, Setup Dock, Setup Dock - Monterey, Setup Dock Ventura, ZZZ_Google Chrome, ZZZ_Microsoft Edge, ZZZ_Slack
- `Jamf Client Communications Doctor`: Jamf Doctor
- `Jamf Client Communications Doctor Test Script`: Jamf Communications Doctor Test
- `Jamf Onboarding Complete`: macOS Onboarding Exclusion
- `Set Dock - Default, Monterey`: Setup Dock - Monterey
- `Set Dock - Default, Ventura`: Setup Dock, Setup Dock Ventura
- `Setup Your Mac`: 01_Setup Your Mac, 02_Setup Your Mac - Auto
- `Setup Your Mac v 1.13.0`: Setup: Icons Install
- `Setup Your Mac v 1.5.1`: ZZZZZZ_01_Computer Setup v1.5.1
- `Setup Your Mac v 1.8.1`: ZZZZZZ_01_Computer Setup v1.8.1
- `swiftDialog Self Service Policy Progress`: Atom (swiftDialog Progress)
- `zz_Quit_SwiftDialog`: Atom, Brave, Chrome, Firefox, Flux, HMA VPN, Handbrake, MS Edge, Silhouette Studio, Tailscale, VLC, VMWare Fusion, Virtual Box, Visual Studio Code, Zoom

## Packages -> Policies

- `ABC Mouse`: ABC Mouse, ABC Mouse Auto Install
- `Activate Office 2024 - New`: Activate Office 2024
- `Erase Install 36.0`: Update macOS
- `Erase Install v. 42.3`: Upgrade to macOS Tahoe
- `Erase-Install v. 39.1`: Upgrade to macOS Monterey, Upgrade to macOS Sequoia, Upgrade to macOS Sonoma, Upgrade to macOS Ventura
- `Icons`: ZZZZZZ_01_Computer Setup v1.5.1, ZZZZZZ_01_Computer Setup v1.8.1
- `Office Activator 2021`: MS Office Install, MS Office Install setup
- `Office Serial Reset`: Office Activation Reset
- `Ruffle`: Ruffle
- `SheepShaver DMG`: SheepShaver
- `Snoopy Screensavers`: Snoopy Screensavers
- `Swift Dialog v. 2.2.1`: Swift Dialog
- `VMware Fusion`: VMWare Fusion

## Categories -> Objects

- `Application`: policy:ABC Mouse, policy:ABC Mouse Auto Install, policy:Activate Office 2024, policy:Atom, policy:Atom (swiftDialog Progress), policy:Brave, policy:Chrome, policy:Firefox, policy:Flux, policy:HMA VPN, policy:Handbrake, policy:MS Edge, policy:MS Edge Setup, policy:MS Office Install, policy:MS Office Install setup, policy:Microsoft Visual Studio Code, policy:Office Activation Reset, policy:Ruffle, policy:SheepShaver, policy:Snoopy Screensavers, policy:Swift Dialog, policy:Tailscale, policy:VLC, policy:VMWare Fusion, policy:Virtual Box, policy:Visual Studio Code, policy:Zoom
- `Application Updates`: policy:App Update - App Auto-Patch, policy:App Update - Brave Browser
- `Ashley`: policy:Asana, policy:Canva, policy:Silhouette Studio
- `No category assigned`: policy:Jamf Doctor, policy:macOS Onboarding Exclusion
- `Printer`: policy:Home Printers
- `Setup Computer`: policy:Setup Dock - Monterey, policy:Setup Dock Ventura, policy:Setup: Icons Install
- `Setup Policies`: policy:00_Rosetta Install, policy:01_Setup Your Mac, policy:02_Setup Your Mac - Auto, policy:Asana Setup, policy:Auto Name Computers, policy:Brave Setup, policy:Chrome Set-up, policy:Chrome Setup, policy:Firefox Setup, policy:Match Machine Name, policy:Name Computer, policy:Setup Dock, policy:Slack Setup, policy:Tailscale Setup, policy:Zoom Setup
- `Testing`: policy:Inventory Update Test - Jamf Communications Dr., policy:Jamf Communications Doctor Test
- `Update`: policy:Cache macOS Monterey, policy:Cache macOS Ventura, policy:Edit Hosts File for Wireless, policy:Issue New File Vault Key, policy:Update Inventory, policy:Update Inventory and Run policies, policy:Update macOS, policy:Upgrade to macOS Monterey, policy:Upgrade to macOS Sequoia, policy:Upgrade to macOS Sonoma, policy:Upgrade to macOS Tahoe, policy:Upgrade to macOS Ventura
- `ZZZ_Depreciated`: policy:ZZZZZZ_01_Computer Setup v1.5.1, policy:ZZZZZZ_01_Computer Setup v1.8.1, policy:ZZZ_Google Chrome, policy:ZZZ_Microsoft Edge, policy:ZZZ_Slack

## Related

- [Policies](object-types/policies.md)
- [Scripts](object-types/scripts.md)
- [Packages](object-types/packages.md)
- [Jamf Object Architecture](../jamf-object-architecture.md)
