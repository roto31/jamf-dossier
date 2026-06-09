# documentation/ Directory

**Path:** `output/documentation/`

Human-readable Markdown documentation generated from exported Jamf objects.

For limits (script bodies, policy scope, smart search criteria, GitHub publish path), see [documentation-coverage-gaps.md](../documentation-coverage-gaps.md).

## Structure

```
output/documentation/
├── api-endpoint-citations.md      # Generated endpoint reference
├── crosslinks.md                  # Policy/script/package relationships
├── policies/
│   ├── README.md                  # Type summary
│   ├── 1__Update_Inventory.md
│   └── ...
├── scripts/
├── packages/
├── computer_groups/
└── ... (one subdirectory per exported object_type)
```

## Root-Level Generated Files

### api-endpoint-citations.md

**Produced by:** `orchestrator.write_endpoint_citations()`

Contains:

- Official Jamf documentation source URLs (`OFFICIAL_SOURCES`)
- Per object type: API family, list/detail paths, reference URL, verification status

Also committed statically at `docs/api-endpoint-citations.md` in the repository.

### crosslinks.md

**Produced by:** `crosslinks.write_crosslink_report()`

Sections:

| Section | Content |
|---------|---------|
| Scripts → Policies | Which policies reference each script name |
| Packages → Policies | Which policies deploy each package |
| Categories → Objects | Category names referenced by policies |

Derived by parsing policy XML (`raw_xml`) for script, package, and category elements. Only includes policies successfully collected in the current run.

## Per-Type Subdirectories

Each `{object_type}/` folder contains:

### README.md

Type-level index with:

- API family
- List and detail endpoint paths
- API reference URL
- Total object count for this run

### Per-Object Files: `{id}__{title}.md`

**Produced by:** `documentation_builder.write_object_documentation()`

Structure of each file:

```markdown
# {object_type} / {id}

## Extracted Summary

```json
{ ... type-specific fields from doc_extractors ... }
```

## Raw Object

```json
{ "raw_xml": "..." }   // Classic API
// or full JSON object   // Jamf Pro API
```
```

#### Extracted Summary Fields by Type

| Object Type | Key Fields |
|-------------|------------|
| policies | enabled, triggers, frequency, scope, scripts, packages |
| computer_groups, mobile_device_groups | is_smart, criteria, members |
| configuration_profiles | uuid, distribution_method, user_removable |
| scripts | language, filename, parameter4 |
| packages | category, filename, priority |
| computer_extension_attributes | data_type, input_type |

For Jamf Pro API JSON-only objects, Extracted Summary may be `{}` because `summarize_xml_object()` only processes XML.

## Stale File Cleanup

Before writing new per-object files, the builder deletes all `*.md` in the type directory except `README.md`. This prevents orphaned docs when objects are removed from Jamf between runs.

## Filename Convention

Same as backup: `{jamf_id}__{safe_name}.md` where `safe_name` sanitizes the object title.

## Reading Guide

1. Start with type `README.md` for endpoint context and counts
2. Open individual `{id}__{title}.md` for object details
3. Use `crosslinks.md` to trace policy dependencies
4. Compare Extracted Summary vs Raw Object when verifying scope or payload settings

## Related Static Docs

Repository-maintained manual gap documentation (not generated at runtime):

- [manual-sso.md](../manual-sso.md)
- [manual-ldap.md](../manual-ldap.md)
- [manual-self-service.md](../manual-self-service.md)
