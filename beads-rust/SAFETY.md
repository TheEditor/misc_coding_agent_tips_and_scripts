# Beads-Rust Safety Model

> How `br` protects your data and integrates safely with version control.

## Core Principle: Non-Invasive

`br` will **never**:
- Execute git commands (no commits, no pushes, no staging)
- Modify files outside `.beads/`
- Install or invoke git hooks
- Run as a daemon or background process
- Auto-commit or auto-push changes
- Connect to external services

Every git operation requires explicit user action.

## Sync Safety Guards

### Export Guards (`br sync --flush-only`)

| Guard | What it prevents | Override |
|-------|------------------|----------|
| **Empty DB guard** | Exporting 0 issues over a JSONL with N issues | `--force` |
| **Stale DB guard** | Exporting when DB is missing issues from JSONL | `--force` |

### Import Guards (`br sync --import-only`)

| Guard | What it prevents | Override |
|-------|------------------|----------|
| **Conflict marker scan** | Importing unresolved git merge conflicts | None - must resolve |
| **Schema validation** | Importing malformed JSON | None - must fix JSONL |

### When to Use `--force`

Use `--force` only when you understand the consequences:
- After deliberately clearing the database
- When JSONL is known to be authoritative
- During recovery from corruption

**Never** use `--force` routinely - it defeats the purpose of the guards.

## History and Backups

br automatically creates backups during `sync --flush-only`:

```bash
br history list              # See available backups
br history restore <backup>  # Restore from backup
```

Backups are stored in `.beads/.br_history/` with timestamps.

## Status Transition Validation

When transitioning to `in_progress` (via `--claim` or `--status in_progress`), br validates the issue is not blocked:

```bash
br update br-42 --claim                    # Fails if blocked
br update br-42 --status in_progress       # Also fails if blocked
br update br-42 --claim --force            # Bypasses the check
```

This prevents accidentally starting work that's blocked by unfinished dependencies.

## Claim Validation

The `--claim` flag also validates assignee:

```bash
br update br-42 --claim  # Fails if assigned to someone else
```

This prevents accidentally stealing work from another agent.

## External JSONL Paths

By default, sync operates on `.beads/issues.jsonl`. To use a different path:

```bash
export BEADS_JSONL=/path/to/issues.jsonl
br sync --flush-only --allow-external-jsonl
```

The `--allow-external-jsonl` flag is required for paths outside `.beads/`.

## Typical Safe Workflow

### Starting a session
```bash
br sync --status           # Check if import needed
br sync --import-only      # Import any JSONL changes from git pull
```

### Ending a session
```bash
br sync --flush-only       # Export DB to JSONL
git add .beads/            # Stage (manual!)
git commit -m "Update issues"
git push
```

### After pulling changes
```bash
git pull
br sync --import-only      # Import collaborators' changes
```

## Error Recovery

### "Refusing to export empty database..."
Your DB has 0 issues but JSONL has existing issues.
- Run `br sync --import-only` first, OR
- Use `--force` if you intentionally want empty export

### "Refusing to export stale database..."
JSONL has issues your DB doesn't.
- Run `br sync --import-only` first, OR
- Use `--force` if you intentionally want to lose those issues

### "Merge conflict markers detected..."
JSONL has unresolved git conflicts (`<<<<<<<`, `=======`, `>>>>>>>`).
- Edit the JSONL and resolve conflicts manually
- `--force` will NOT bypass this check

## Defense in Depth

br employs multiple protection layers:

| Layer | Protection |
|-------|------------|
| No git operations | Cannot execute `git rm`, `git clean`, etc. |
| Path confinement | All writes confined to `.beads/` |
| Atomic writes | Temp file + rename prevents partial corruption |
| Safety guards | Destructive operations require explicit `--force` |
| Automatic backups | History preserved before overwrites |
