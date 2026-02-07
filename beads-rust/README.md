# Beads-Rust Best Practices

This folder adapts the Beads issue-writing guidance for the `beads_rust` tool (`br`).

## Operational Differences vs classic Beads

- `br` is non-invasive: it never runs git commands.
- Sync is explicit. Before commit/push: run `br sync --flush-only`, then `git add .beads/` and commit. After pull (or when JSONL is newer): run `br sync --import-only`.
- `.beads/beads.db` is the primary store and `.beads/issues.jsonl` is the export. Keep JSONL under version control; treat the DB as local unless your repo policy says otherwise.
- Set the issue prefix to `br` so IDs match these docs: `br config set id.prefix=br` (or set `id.prefix: "br"` in `.beads/config.yaml`).
- `br create` captures title/description (plus metadata). Use `br update` to add `design`, `acceptance`, and `notes`.

## Command Reminders

- Day-to-day: `br create`, `br show`, `br list`, `br ready`, `br update`, `br close`, `br dep add`.
- Quick capture: `br q` creates a placeholder issue you can flesh out later.
- Agent help: `br agents --add --force` writes AGENTS.md.

## Documents

- `CODE_IN_ISSUES.md`: what code belongs in issues
- `ISSUE_DESIGN.md`: how to separate description/design/acceptance/notes
- `FIELD_SEMANTICS.md`: field-by-field reference
- `EXAMPLES.md`: before/after issue writing patterns
- `WHEN_TO_CREATE_ISSUES.md`: how to file discovered work
