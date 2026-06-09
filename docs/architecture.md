# Architecture

## System Architecture

```mermaid
flowchart TB
  subgraph cli [CLI Layer]
    RunScript["scripts/run_full_export.py"]
    AuxScripts["scripts/*.py"]
  end

  subgraph orchestrator [Orchestration]
    Orch["jamf_exporter.orchestrator.run_full_export"]
  end

  subgraph core [Core Modules]
    Config["config.RuntimeConfig"]
    Auth["auth.JamfTokenProvider"]
    Client["http_client.JamfApiClient"]
    Registry["endpoint_registry"]
    Filter["registry_filter"]
    Collector["collectors.generic"]
    Inventory["collectors.inventory"]
    Binaries["binaries.package_fetcher"]
    SSH["binaries.ssh_fetcher"]
    DocBuilder["documentation_builder"]
    BackupWriter["backup_writer"]
    Manifest["manifest"]
    DRManifest["dr.manifest"]
    Gaps["gaps"]
    Failures["failures"]
    Crosslinks["crosslinks"]
    Compat["compatibility"]
    Vault["secrets.vault"]
  end

  subgraph restore [Restore - Optional]
    RestoreOrch["restore.orchestrator"]
    Preflight["restore.preflight"]
    Ordering["restore.ordering"]
  end

  subgraph external [External]
    JamfPro["Jamf Pro Server"]
    JSSSSH["Jamf Server SSH"]
  end

  subgraph output [Output Artifacts]
    Backup["output/backup/"]
    Docs["output/documentation/"]
    ManifestOut["output/manifest/"]
    GapsOut["output/gaps/"]
    Logs["output/logs/"]
    InvOut["output/inventory/"]
    BinOut["output/binaries/"]
    SrvOut["output/server/"]
    SecOut["output/secrets/"]
  end

  RunScript --> Orch
  AuxScripts --> Orch
  Orch --> Config
  Orch --> Auth
  Orch --> Client
  Orch --> Registry
  Registry --> Filter
  Filter --> Collector
  Client --> JamfPro
  Auth --> JamfPro
  Collector --> DocBuilder
  Collector --> BackupWriter
  DocBuilder --> Docs
  BackupWriter --> Backup
  Orch --> Manifest
  Orch --> DRManifest
  Orch --> Gaps
  Orch --> Failures
  Orch --> Crosslinks
  Orch --> Compat
  Orch --> Inventory
  Inventory --> InvOut
  Orch --> Binaries
  Binaries --> BinOut
  Binaries --> Client
  Binaries --> SSH
  SSH --> JSSSSH
  SSH --> SrvOut
  Vault --> SecOut
  Manifest --> ManifestOut
  Gaps --> GapsOut
  Orch --> Logs
  RestoreOrch --> Client
```

## Authentication Flow

```mermaid
flowchart TD
  Start["run_full_export()"] --> LoadEnv["RuntimeConfig.from_env()"]
  LoadEnv --> Preflight["token_provider.get_token()"]
  Preflight --> OAuthCheck{"client_id AND client_secret set?"}
  OAuthCheck -->|Yes| OAuthReq["POST /api/v1/oauth/token"]
  OAuthCheck -->|No| BasicCheck{"username AND password set?"}
  OAuthReq --> OAuthOK{"status 200?"}
  OAuthOK -->|No| OAuthBody["Retry with client_id/secret in form body"]
  OAuthBody --> OAuthBodyOK{"status 200?"}
  OAuthBodyOK -->|No| BasicCheck
  OAuthOK -->|Yes| TokenReady["Bearer token cached"]
  OAuthBodyOK -->|Yes| TokenReady
  BasicCheck -->|Yes| BasicReq["POST /api/v1/auth/token"]
  BasicCheck -->|No| AuthFail["AuthError exit code 2"]
  BasicReq --> BasicOK{"status 200?"}
  BasicOK -->|No| AuthFail
  BasicOK -->|Yes| TokenReady
  TokenReady --> ProbeVer["Probe version + deployment"]
  ProbeVer --> FilterLoop["filter_specs_for_run()"]
  FilterLoop --> CollectLoop["Collect active endpoint specs"]
```

Source: `jamf_exporter/auth.py`, `jamf_exporter/orchestrator.py` lines 134–146.

## Per-Object Collection Pipeline

```mermaid
flowchart LR
  AllSpecs["41 EndpointSpec rows"] --> Filter["filter_specs_for_run()"]
  Filter --> Spec["Active EndpointSpec"]
  Spec --> ListReq["GET list_path"]
  ListReq --> StatusCheck{"status >= 400?"}
  StatusCheck -->|Yes| RecordErr["Record error, return empty"]
  StatusCheck -->|No| ParseIDs["Parse object IDs"]
  ParseIDs --> DetailCheck{"detail_path set?"}
  DetailCheck -->|No| Singleton["Return singleton object"]
  DetailCheck -->|Yes| DetailLoop["For each ID"]
  DetailLoop --> DetailReq["GET detail_path"]
  DetailReq --> ObjectDict["Object dict with id, name, payload"]
  ObjectDict --> WriteDoc["write_object_documentation()"]
  ObjectDict --> WriteBackup["write_backups()"]
  WriteDoc --> MDFile["id__title.md"]
  WriteBackup --> RawFile["id__title.json|xml"]
  WriteBackup --> ExportRecord["ExportRecord → manifest"]
```

Source: `jamf_exporter/collectors/generic.py`, `jamf_exporter/documentation_builder.py`, `jamf_exporter/backup_writer.py`.

## Full Backup Tier Pipeline

When `full_backup=True` (CLI: `--full`; see [Usage Guide](usage-guide.md)):

```mermaid
flowchart TD
  MetadataDone["Metadata tier complete"] --> FullCheck{"full_backup?"}
  FullCheck -->|No| Finalize
  FullCheck -->|Yes| InvCollect["collect_inventory()"]
  InvCollect --> InvOut["inventory/computers/, mobile_devices/, filevault_keys.csv"]
  InvCollect --> SSHCheck{"JAMF_SSH_HOST and JAMF_SSH_USER set?"}
  SSHCheck -->|Yes| SSHFetch["fetch_jss_filesystem()"]
  SSHFetch --> SrvOut["server/filesystem/cache-manifest.json"]
  SSHCheck -->|Yes| PkgOnPrem["fetch_package_binaries(on-prem + SSH)"]
  SSHCheck -->|No| PkgCloud["fetch_package_binaries(cloud or on-prem without SSH)"]
  PkgOnPrem --> BinOut["binaries/packages/"]
  PkgCloud --> BinOut
  PkgOnPrem --> PkgManifest["binaries/manifest.json"]
  PkgCloud --> PkgManifest
  InvOut --> DRBuild["build_dr_manifest()"]
  SrvOut --> DRBuild
  BinOut --> DRBuild
  DRBuild --> DRFile["manifest/dr-manifest.json"]
  DRFile --> Finalize["write_manifest, gaps, crosslinks, etc."]
```

Source: `jamf_exporter/orchestrator.py` lines 188–225.

## HTTP Client Retry Logic

```mermaid
flowchart TD
  Request["JamfApiClient.request()"] --> AttachToken["Attach Bearer token"]
  AttachToken --> Send["session.request()"]
  Send --> StatusCheck{"status == 401?"}
  StatusCheck -->|Yes, attempt 1| RefreshToken["Clear token, re-fetch"]
  RefreshToken --> Send
  StatusCheck -->|No| SuccessCheck{"status < 500?"}
  SuccessCheck -->|Yes| Return["Return response"]
  SuccessCheck -->|No| RetryCheck{"attempts remaining?"}
  StatusCheck -->|Yes, attempt > 1| RetryCheck
  RetryCheck -->|Yes| Backoff["Sleep retry_backoff * attempt"]
  Backoff --> Send
  RetryCheck -->|No| Return
```

Source: `jamf_exporter/http_client.py`.

## Restore Preview Flow

```mermaid
flowchart TD
  Start["restore_from_bundle()"] --> LoadDR["Read manifest/dr-manifest.json"]
  LoadDR --> LoadRecords["load_manifest_records()"]
  LoadRecords --> Preflight["run_preflight()"]
  Preflight --> Gates{"dry_run?"}
  Gates -->|No| ValidateGates["Check JAMF_ALLOW_RESTORE, JAMF_RESTORE_TARGET_URL, lab_confirmed"]
  Gates -->|Yes| TopoSort["topo_sort(object_types)"]
  ValidateGates --> TopoSort
  TopoSort --> Preview["Write restore/preview.md"]
  Preview --> Done["Return preview path"]
```

Source: `jamf_exporter/restore/orchestrator.py`.

## Exit Codes

| Code | Condition | Source |
|------|-----------|--------|
| `0` | Export completed with zero errors | `orchestrator.py` line 253 |
| `1` | Export completed but one or more collector/API errors occurred | `orchestrator.py` line 253 |
| `2` | Authentication preflight failed | `orchestrator.py` line 146 |

## Legacy vs Production Path

| Aspect | Production (`jamf_exporter/`) | Legacy (`src/`) |
|--------|-------------------------------|-----------------|
| Config | Environment variables | JSON config file |
| Auth | OAuth + Basic fallback | Basic-to-token only |
| Output layout | `output/documentation/`, `output/backup/` | `output/docs/`, `output/raw/` |
| Catalog | `endpoint_registry.py` (41 specs) | `config/endpoint_catalog.json` |
| DR bundle | Yes (v2.0) | No |
| Legacy mirrors | `raw/`, `docs/`, `manifests/`, `gap-report.md` | Primary paths |

The production path is invoked via `scripts/run_full_export.py`. Legacy scripts remain for reference but are not the recommended entrypoint.

Compatibility mapping is documented in `manifest/compatibility-spec.json` (produced by `jamf_exporter/compatibility.py`).
