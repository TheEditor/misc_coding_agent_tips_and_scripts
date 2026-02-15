# Beads-Rust Best Practices

Operational guidance for AI agents using the `beads_rust` (`br`) issue tracker.

## Operational Differences vs classic Beads

- `br` is non-invasive: it never runs git commands.
- Sync is explicit. Before commit/push: run `br sync --flush-only`, then `git add .beads/` and commit. After pull (or when JSONL is newer): run `br sync --import-only`.
- `.beads/beads.db` is the primary store and `.beads/issues.jsonl` is the export. Keep JSONL under version control; treat the DB as local unless your repo policy says otherwise.
- Set the issue prefix to `br` so IDs match these docs: `br config --set id.prefix=br` (or set `id.prefix: "br"` in `.beads/config.yaml`).
- `br create` captures title/description (plus metadata). Use `br update` to add `design`, `acceptance`, and `notes`.

## Hierarchical Child IDs

When creating child issues with `--parent`, br generates hierarchical IDs:

```bash
br create "Epic: Auth system" --type epic    # → br-abc123
br create "Implement login" --parent br-abc123   # → br-abc123.1
br create "Implement logout" --parent br-abc123  # → br-abc123.2
br create "Add OAuth" --parent br-abc123         # → br-abc123.3
```

This makes epic decomposition visible in the ID structure itself.

## Atomic Claim Workflow

Use `--claim` to atomically take ownership of an issue:

```bash
br update br-42 --claim
```

This sets `status=in_progress` AND `assignee=$BD_ACTOR` in one operation. It also validates:
- Fails if already assigned to someone else
- Fails if issue is blocked by open dependencies (use `--force` to override)

The blocked-issue check also applies to `--status in_progress` without `--claim`.

## Command Reminders

- Day-to-day: `br create`, `br show`, `br list`, `br ready`, `br update`, `br close`, `br dep add`.
- Quick capture: `br q` creates a placeholder issue you can flesh out later.
- Agent help: `br agents --add --force` writes AGENTS.md.
- Claim work: `br update <id> --claim` (atomic status + assignee).
- Chained work: `br close <id> --suggest-next` returns newly unblocked issues.

## Output Formats

br supports multiple output formats:

| Flag | Use case |
|------|----------|
| `--json` | Machine-readable, stable schema |
| `--format toon` | Token-efficient for LLM context |
| (default) | Human-readable with colors (TTY) or plain text (piped) |

Always use `--json` when parsing programmatically.

## Documents

- `CODE_IN_ISSUES.md`: what code belongs in issues
- `ISSUE_DESIGN.md`: how to separate description/design/acceptance/notes
- `FIELD_SEMANTICS.md`: field-by-field reference
- `EXAMPLES.md`: before/after issue writing patterns
- `ISSUES_VS_PLANNING_DOCS.md`: why issues should be self-contained, not cross-reference plan files
- `WHEN_TO_CREATE_ISSUES.md`: how to file discovered work
- `SAFETY.md`: sync guards, history, and data protection
