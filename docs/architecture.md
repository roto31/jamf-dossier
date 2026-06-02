# Architecture

## System Architecture

```mermaid
flowchart TB
  subgraph cli [CLI Layer]
    RunScript["scripts/run_full_export.py"]
  end

  subgraph orchestrator [Orchestration]
    Orch["jamf_exporter.orchestrator.run_full_export"]
  end

  subgraph core [Core Modules]
    Config["config.RuntimeConfig"]
    Auth["auth.JamfTokenProvider"]
    Client["http_client.JamfApiClient"]
    Registry["endpoint_registry"]
    Collector["collectors.generic"]
    DocBuilder["documentation_builder"]
    BackupWriter["backup_writer"]
    Manifest["manifest"]
    Gaps["gaps"]
    Crosslinks["crosslinks"]
  end

  subgraph external [External]
    JamfPro["Jamf Pro Server"]
  end

  subgraph output [Output Artifacts]
    Docs["output/documentation/"]
    Backup["output/backup/"]
    ManifestOut["output/manifest/"]
    GapsOut["output/gaps/"]
    Logs["output/logs/"]
  end

  RunScript --> Orch
  Orch --> Config
  Orch --> Auth
  Orch --> Client
  Orch --> Registry
  Registry --> Collector
  Client --> JamfPro
  Auth --> JamfPro
  Collector --> DocBuilder
  Collector --> BackupWriter
  DocBuilder --> Docs
  BackupWriter --> Backup
  Orch --> Manifest
  Orch --> Gaps
  Orch --> Crosslinks
  Manifest --> ManifestOut
  Gaps --> GapsOut
  Orch --> Logs
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
  TokenReady --> CollectLoop["Iterate endpoint specs"]
```

Source: `jamf_exporter/auth.py`, `jamf_exporter/orchestrator.py` lines 102–114.

## Per-Object Collection Pipeline

```mermaid
flowchart LR
  Spec["EndpointSpec"] --> ListReq["GET list_path"]
  ListReq --> ParseIDs["Parse object IDs"]
  ParseIDs --> DetailLoop["For each ID"]
  DetailLoop --> DetailReq["GET detail_path"]
  DetailReq --> ObjectDict["Object dict with id, name, payload"]
  ObjectDict --> WriteDoc["write_object_documentation()"]
  ObjectDict --> WriteBackup["write_backups()"]
  WriteDoc --> MDFile["id__title.md"]
  WriteBackup --> RawFile["id__title.json|xml"]
  WriteBackup --> ExportRecord["ExportRecord → manifest"]
```

Source: `jamf_exporter/collectors/generic.py`, `jamf_exporter/documentation_builder.py`, `jamf_exporter/backup_writer.py`.

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

## Exit Codes

| Code | Condition | Source |
|------|-----------|--------|
| `0` | Export completed with zero errors | `orchestrator.py` line 162 |
| `1` | Export completed but one or more collector/API errors occurred | `orchestrator.py` line 162 |
| `2` | Authentication preflight failed | `orchestrator.py` line 114 |

## Legacy vs Production Path

| Aspect | Production (`jamf_exporter/`) | Legacy (`src/`) |
|--------|-------------------------------|-----------------|
| Config | Environment variables | JSON config file |
| Auth | OAuth + Basic fallback | Basic-to-token only |
| Output layout | `output/documentation/`, `output/backup/` | `output/docs/`, `output/raw/` |
| Catalog | `endpoint_registry.py` | `config/endpoint_catalog.json` |

The production path is invoked via `scripts/run_full_export.py`. Legacy scripts remain for reference but are not the recommended entrypoint.
