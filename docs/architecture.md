# Architecture

## System architecture

```mermaid
flowchart TB
  subgraph app [Jamf Dossier macOS App]
    UI["SwiftUI Views"]
    Settings["AppSettings + Keychain"]
    VM["BackupViewModel"]
    Orch["ExportOrchestrator"]
    Registry["EndpointRegistry"]
  end

  subgraph external [External]
    JamfPro["Jamf Pro HTTPS API"]
    Keychain["macOS Keychain"]
    SSH["Jamf Server SSH"]
  end

  subgraph output [Backup Folder]
    Backup["backup/"]
    Docs["documentation/"]
    Manifest["manifest/"]
    Gaps["gaps/"]
    Logs["logs/"]
    DR["inventory/, binaries/, server/"]
  end

  UI --> VM
  VM --> Orch
  Settings --> Keychain
  Settings --> Orch
  Orch --> Registry
  Orch --> JamfPro
  Orch --> SSH
  Orch --> Backup
  Orch --> Docs
  Orch --> Manifest
  Orch --> Gaps
  Orch --> Logs
  Orch --> DR
```

## Authentication flow

```mermaid
flowchart TD
  Start["Run Backup"] --> LoadKC["Load credentials from Keychain"]
  LoadKC --> OAuthCheck{"OAuth client configured?"}
  OAuthCheck -->|Yes| OAuthReq["POST /api/v1/oauth/token"]
  OAuthCheck -->|No| BasicReq["POST /api/v1/auth/token"]
  OAuthReq --> TokenReady["Bearer token"]
  BasicReq --> TokenReady
  TokenReady --> Probe["GET /api/v1/jamf-pro-version"]
  Probe --> Loop["For each of 41 endpoint specs"]
```

## Per-object collection

```mermaid
flowchart LR
  Spec["EndpointSpec"] --> List["GET list"]
  List --> IDs["Parse IDs"]
  IDs --> Detail["GET detail per ID"]
  Detail --> Raw["Write backup/ + documentation/"]
  Raw --> ManifestRow["manifest.json row + SHA-256"]
```

## Optional DR tiers

```mermaid
flowchart TD
  Meta["Metadata export complete"] --> Inv{"Include inventory?"}
  Inv -->|Yes| InvAPI["computers-inventory, mobile-devices, FileVault"]
  Inv --> SSH{"SSH configured?"}
  SSH -->|Yes| MySQL["Server Tools MySQL backup"]
  SSH -->|Yes| Tomcat["Tomcat config copy"]
  SSH -->|Yes| PkgSCP["Package binaries via SCP"]
  Inv -->|No| PkgCloud["Package binaries via JCDS API"]
  InvAPI --> DRMan["dr-manifest.json tiers_completed"]
  MySQL --> DRMan
  PkgSCP --> DRMan
  PkgCloud --> DRMan
```

## Data flow summary

| Stage | Input | Output |
|-------|-------|--------|
| Auth | Keychain credentials | Bearer token |
| Collect | 41 API endpoints | `backup/`, `documentation/` |
| Inventory | JPAPI device endpoints | `inventory/` |
| Binaries | Package metadata + SSH or JCDS | `binaries/packages/` |
| Finalize | All records + errors | `manifest/`, `gaps/`, `logs/` |

## Repository vs application

| This repo | Shipped app |
|-----------|-------------|
| Documentation, CHANGELOG, release checksums | Signed `.app` in DMG |
| `endpoint_registry.json` reference copy | Registry inside app bundle |
| No Swift source | Proprietary binary |

## Related

- [Export Engine](export-engine.md)
- [Output Directory](output/index.md)
- [DR Overview](dr/README.md)
