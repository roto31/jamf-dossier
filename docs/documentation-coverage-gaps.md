# Documentation and backup coverage — findings

**As of:** 2026-06-08 (v0.4.0 gap closure)  
**Audience:** Operators and developers evaluating Jamf Dossier / `jamf_exporter` exports for GitHub-readable documentation.  
**Related:** [documentation-directory.md](output/documentation-directory.md), [backup-directory.md](output/backup-directory.md), [project-executive-summary.md](project-executive-summary.md)

---

## Executive summary

| Area | Backup fidelity (`backup/`) | Human-readable docs (`documentation/`, wiki) |
|------|------------------------------|-----------------------------------------------|
| **Scripts** | **High** — full source in Classic API XML | **High** — `## Script Source` in per-object markdown (Python + Swift); base64 `script_contents_encoded` fallback |
| **Policies** | **High** — full policy XML | **High** — plain-text extractors with scope, scripts, packages, Mermaid diagrams |
| **Smart computer groups** | **High** — criteria in XML | **High** — criteria prose in Python and Swift extractors |
| **Advanced computer searches** | **High** — criteria in XML | **High** — dedicated extractors with criteria and display fields |
| **GitHub upload** | N/A | **Optional** — `publish_lha_backup.py --github-push` or GitHub Actions workflow |

**Practical rule:** **`backup/`** remains authoritative for DR. **`documentation/`** is now a readable mirror for scripts, policies, groups, and advanced searches — verify with `scripts/verify_documentation_markdown.sh`.

---

## Scripts — is 100% of the script text backed up?

### Backup: yes

The Classic API script detail response includes the full script body in:

- `<script_contents>` — plain script source
- `<script_contents_encoded>` — base64 duplicate (when present)

`jamf_exporter/backup_writer.py` writes the entire detail response to:

```text
backup/scripts/{jamf_id}__{safe_name}.xml
```

No truncation is applied in the writer; content is the API’s `raw_xml` string.

### Documentation: no (as readable plain text)

Per-object files under `documentation/scripts/` are produced by `documentation_builder.write_object_documentation()` (Python) and `DocumentationBuilder` (Swift). Each file contains:

1. **Extracted Summary** — small JSON block.
2. **Raw Object** — JSON wrapping the full XML (escaped), not a dedicated script section.

#### Python extracted fields (`jamf_exporter/doc_extractors.py`)

For `scripts` only:

- `language` (from `.//script_info/language` — often empty; Jamf may use other paths)
- `filename`
- `parameter4`

**Not extracted into summary:** `script_contents`, `script_contents_encoded`, notes, parameters 5–11, OS requirements.

#### Swift extracted fields (`JamfBackup/.../DocSummaryExtractor.swift`)

Even thinner:

- `id`, `name`
- `enabled` / `frequency` only for policies (not scripts’ body)
- For scripts: regex on `<language>` and `<filename>` only

**Swift does not read `<script_contents>`.**

#### Example (real export)

`documentation/scripts/1__00_Prepare_SwiftDialog.sh.md` shows Extracted Summary with only `filename`, `id`, `name`. The full zsh source appears only inside the escaped `raw_xml` string under **Raw Object** — difficult to read or diff on GitHub.

### Verdict

| Question | Answer |
|----------|--------|
| Is 100% of script text retrieved from Jamf? | **Yes**, in `backup/scripts/*.xml` when the API returns it |
| Is 100% shown as human-readable markdown? | **No** |
| Best source for script DR / review | **`backup/scripts/`** — open XML or parse `<script_contents>` |

---

## Policies — scope, scripts, packages, and “every piece”

### Backup: yes (full XML)

Each policy is stored as complete Classic API XML in `backup/policies/{id}__{name}.xml`, including:

- General (triggers, frequency, category, network limits, etc.)
- Scope (`all_computers`, computers, **computer_groups**, buildings, departments, limitations, exclusions)
- Self Service
- Package configuration
- Scripts (with parameters)
- Maintenance, reboot, files/processes, user interaction, disk encryption, printers, dock items, account maintenance, etc.

### Extracted summary (Python — `doc_extractors.py`)

When documentation is built through the **Python** exporter, policy summaries include:

| Field | Source in XML | In summary? |
|-------|----------------|-------------|
| `enabled` | `.//general/enabled` | Yes |
| `trigger_checkin`, `trigger_enrollment_complete` | general | Yes |
| `frequency` | general | Yes |
| `scripts` | `.//scripts/script/name` | Yes (names) |
| `packages` | `.//package_configuration/packages/package/name` | Yes (names) |
| `scope_targets` | `.//scope/computers/computer/name` | Yes (computer names only) |
| `scope_exclusions` | `.//scope/exclusions/computers/computer/name` | Yes (computer names only) |

**Not in Python summary:**

- Scoped **computer groups** (static or smart)
- `all_computers` / `all_jss_users` flags
- Buildings, departments, user groups, network segments, iBeacons
- Script **parameters** (parameter4–11 per script row)
- Self Service settings, maintenance, reboot, printers, dock, etc.
- Mac App Store apps as separate “applications” (packages are listed; MAS apps are a different object type)

### Extracted summary (Swift — `DocSummaryExtractor.swift`)

Policy per-object docs from **Jamf Dossier app** exports often show only:

- `id`, `name`, `enabled`, `frequency`

Scripts, packages, and scope are **omitted** from Extracted Summary even when present in Raw Object.

### Crosslinks (`jamf_exporter/crosslinks.py` → `documentation/crosslinks.md`)

Reverse maps only:

- **Scripts → Policies** (which policies reference each script name)
- **Packages → Policies**
- **Categories → Objects** (policy category names)

Does **not** document:

- Policy → scoped groups
- Policy → individual computers (except via raw XML)
- Full policy composition

### Wiki-style tables (`publish_lha_backup.py` + `output_indexes.py`)

After `scripts/publish_lha_backup.py`, `docs/lotus-home-academy/object-types/policies.md` includes **plain-text one-liners** per policy, e.g.:

> Policy `Brave Setup` (enabled=true, Ongoing). Scripts: Installomater v 10.5. Packages: none.

Useful for inventory, but still **not**:

- Scoped smart/static groups
- Every policy subsection in prose
- Script parameters per policy row

### Per-object markdown (`documentation/policies/*.md`)

Structure matches all Classic types:

```markdown
## Extracted Summary
```json
{ ... thin fields ... }
```

## Raw Object
```json
{ "raw_xml": "<?xml ... entire policy ...>" }
```
```

Full policy is recoverable from **Raw Object** or **backup XML**, not from Extracted Summary alone.

### Verdict

| Question | Answer |
|----------|--------|
| Are scripts/packages in policy docs? | **Partially** — Python summary + crosslinks + wiki one-liners; **not** full Swift summary |
| Are scoped **groups** shown? | **No** in summaries (only in raw XML / backup) |
| Is every policy section documented in plain text? | **No** — only a subset in Python JSON; rest in raw XML only |

---

## Smart computer groups (`computer_groups`)

### Backup

Full group XML in `backup/computer_groups/`, including for smart groups:

- `<is_smart>true</is_smart>`
- `<criteria><criterion>...</criterion></criteria>` (name, search_type, value, and_or, priority, parentheses)
- `<computers>` membership list when returned by API

### Python extractor

`doc_extractors.py` builds:

```json
"criteria": [
  { "name": "...", "search_type": "...", "value": "..." }
],
"is_smart": "true|false",
"members": [ "computer names..." ]
```

Rendered in per-object docs as **JSON inside markdown**, not English sentences.

`output_indexes.summarize_record()` produces index lines like:

> Smart group `macOS Out of Date` with 2 criteria and 0 listed members.

**Criterion text is not spelled out** in the index table.

### Swift extractor

**No** criteria or `is_smart` in Extracted Summary — only `id` and `name` in observed Swift exports, despite criteria present in `raw_xml`.

### Verdict

| Question | Answer |
|----------|--------|
| Are criteria stored? | **Yes** in backup XML |
| Are criteria human-readable on GitHub? | **No** — JSON blob or raw XML only; Swift summaries especially weak |

---

## Advanced computer searches (`advanced_computer_searches`)

### Backup

Full search XML in `backup/advanced_computer_searches/`, including:

- Multiple `<criterion>` rows (name, search_type, value, and_or, opening_paren, closing_paren, priority)
- `<display_fields>`
- `<computers>` result snapshot (when API includes it)

### Extractor gap

`doc_extractors.py` has **no** `elif object_type == "advanced_computer_searches"` (or mobile equivalent). Summaries fall through to generic `id` + `name` only.

Same gap in Swift `DocSummaryExtractor`.

### Documentation

Per-object files contain full criteria only inside **Raw Object** escaped XML JSON — same readability problem as policies.

### Verdict

| Question | Answer |
|----------|--------|
| Are search criteria backed up? | **Yes** (`backup/`) |
| Plain-text criteria in docs? | **No** dedicated extraction or prose |

---

## Mobile device groups and advanced mobile device searches

Same patterns as computer-side types where exported:

- **Backup:** full XML/JSON per registry entry
- **Python:** `mobile_device_groups` uses the same criteria/members branch as `computer_groups`
- **Advanced mobile device searches:** same extractor gap as advanced computer searches (no dedicated summary branch)

---

## Format: “human readable text” vs what the tool emits

### What “documentation” means today

| Output path | Format | Readability |
|-------------|--------|-------------|
| `documentation/{type}/{id}__{name}.md` | Markdown wrapping **JSON** blocks | Poor for long XML; good for machines |
| `docs/{type}.md` (legacy) | Aggregated JSON per object | Poor |
| `documentation/crosslinks.md` | Markdown lists | Good for script/package ↔ policy |
| `docs/lotus-home-academy/object-types/*.md` | Markdown **tables** + one-line summaries | Good for inventory; not full object detail |
| `backup/{type}/*.{xml,json}` | Raw API payloads | Best for completeness; XML needs viewer |

The pipeline was designed for **manifest parity, restore, and DR**, not for publishing full Jamf config as readable prose on GitHub.

### Redaction

`redaction.redact_sensitive_data()` masks secrets in documentation JSON (passwords, tokens, etc.). Script bodies and most policy fields are **not** redacted.

---

## GitHub — what gets uploaded automatically?

### Does not happen on export alone

A normal backup (Jamf Dossier app or `run_full_export.py`) writes to a **local folder** (or timestamped run under a bookmarked destination). Nothing is pushed to GitHub unless you do it separately.

### Paths to GitHub-readable content

| Mechanism | What it publishes | Repo typical target |
|-----------|---------------------|---------------------|
| `scripts/publish_lha_backup.py` | Copies export → `Lotus-Home-Academy-Backup/` (or `--destination`); regenerates `docs/lotus-home-academy/object-types/*.md` | **This** private analysis repo (if committed) |
| `jamf_exporter/site_wiki.py` | Site wiki pages under `docs/lotus-home-academy/` | Committed markdown |
| `scripts/sync_public_docs.sh` | Product documentation mirror | **`roto31/jamf-dossier`** — not tenant backup |
| `scripts/sync_public_wiki.sh` | GitHub Wiki pages | Public wiki |
| Manual `git add` / `git push` | Any export output directory | Operator choice |

**Public Jamf Dossier releases** ship the macOS app DMG and docs; they do **not** include your Jamf Pro tenant’s policies, scripts, or groups.

---

## Python vs Swift export (documentation quality)

| Capability | Python (`jamf_exporter`) | Swift (Jamf Dossier app) |
|------------|--------------------------|---------------------------|
| Policy scripts/packages in Extracted Summary | Yes (`doc_extractors`) | No (only enabled/frequency) |
| Policy scope computer names | Yes | No |
| Script body in summary | No | No |
| Smart group criteria in summary | Yes (JSON) | No |
| Advanced search criteria in summary | No | No |
| `crosslinks.md` | Yes (if policies collected) | Same when orchestrator writes it |
| Wiki tables via `publish_lha_backup.py` | Yes (post-process) | Requires publishing Python-indexed backup or re-run publish on bundle |

**Recommendation:** For documentation destined for GitHub review, run **Python** `scripts/run_full_export.py` (or API `run_full_export`), then **`scripts/publish_lha_backup.py`**, then commit. Using **Swift-only** exports alone produces the thinnest `documentation/` summaries.

---

## Code references (implementation)

| Concern | Module |
|---------|--------|
| Raw backup write | `jamf_exporter/backup_writer.py`, `JamfBackup/.../BackupWriter.swift` |
| Per-object markdown | `jamf_exporter/documentation_builder.py`, `JamfBackup/.../DocumentationBuilder.swift` |
| XML field extraction (Python) | `jamf_exporter/doc_extractors.py` |
| XML field extraction (Swift) | `JamfBackup/.../DocSummaryExtractor.swift` |
| Policy crosslinks | `jamf_exporter/crosslinks.py` |
| Human one-liners in tables | `jamf_exporter/output_indexes.py` → `summarize_record()`, `_summary_from_extracted()` |
| Site publish to repo docs | `scripts/publish_lha_backup.py`, `jamf_exporter/site_wiki.py` |
| Doc layout spec | `docs/output/documentation-directory.md` |

---

## Gaps vs operator expectations (checklist)

Implementation status (2026-06-04):

- [x] **Script source** as fenced code blocks — `jamf_exporter/extractors/script_extractor.py`
- [x] **Policy scope** groups + computers — `jamf_exporter/extractors/policy_extractor.py`
- [x] **Policy scripts** with parameters — same module
- [x] **Policy sections** maintenance/reboot/self service/files — same module
- [x] **Smart group criteria** as prose lines — `group_extractor.py` + `criteria_prose.py`
- [x] **Advanced search criteria** — `advanced_search_extractor.py`
- [x] **Swift parity** — `PlainTextDocumentation.swift`, enhanced `DocSummaryExtractor.swift`
- [x] **GitHub publish** — `scripts/publish_lha_backup.py --github-push`, `.github/workflows/publish-backup.yml`
- [x] **Verify script** — `scripts/verify_documentation_markdown.sh`

---

## Honest scope (unchanged)

On-prem + MySQL restore remains the closest path to full Jamf fidelity. Cloud export and API backup are **not** a literal clone; devices may require re-enrollment. Documentation gaps above are about **readability and publishing**, not about missing raw backup payloads for Classic API object types that exported successfully.

---

## One-line summary

**Backup/` remains authoritative for DR; `documentation/` now includes Plain-Text and Mermaid sections (Python export + publish). Swift-only exports may still be thinner until parity is run through the same publish path.**
