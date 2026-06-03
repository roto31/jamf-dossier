# Archive policy

## Principle

Do not delete tracked project content without archiving it under `Archive/removals/`.

## When to archive

- Removing directories or files that were previously committed
- Retiring features, scripts, or large build artifacts mistakenly checked in

## Exclusions (no archive required)

- `.venv/`, `__pycache__/`, local `config/export.env`
- Ephemeral paths already in `.gitignore` that were never tracked

## Procedure

1. Run `scripts/archive_removed_paths.sh --slug NAME --reason "..." --paths PATH ...`
2. Commit the `Archive/removals/...` entry
3. Remove paths from the active tree (`git rm` or delete + commit)
4. Same PR must include both archive and removal

## Naming

- Slug: lowercase, hyphens, descriptive (`jamfbackup-swiftpm-build`)
- Folder: `YYYY-MM-DD_<slug>`

## PR checklist

- [ ] Archive entry exists for every tracked deletion
- [ ] `MANIFEST.json` lists original paths and commit
- [ ] `RESTORE.md` documents restore steps
