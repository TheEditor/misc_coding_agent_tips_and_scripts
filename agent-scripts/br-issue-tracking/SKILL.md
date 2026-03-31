---
name: br-issue-tracking
description: Track multi-session work with beads_rust (br) issue tracker, including creating/updating/closing issues, managing dependencies, syncing JSONL/DB, configuring prefixes, and migrating bd workflows to br. Use when a project uses .beads/ with br or when converting bd instructions to br.
---

# br Issue Tracking (beads_rust)

## Start a session

- Check for `.beads/` in the repo. If missing, run `br init` in the repo root.
- Run `br sync --status` to check if import is needed; if so, run `br sync --import-only`.
- Run `br ready --json` to see unblocked work and `br list --status in_progress --json` to see claimed work.
- If any `in_progress` issues exist, run `br show <id>` and summarize notes to the user before proceeding.

## Claim and complete work

**Claim with `--claim`** (recommended):
```bash
br update <id> --claim
```
This atomically sets `status=in_progress` and `assignee` to the current actor. It also validates:
- Fails if already assigned to someone else
- Fails if blocked by open dependencies (use `--force` to override)

**Close with traceable `--reason`** (include what changed + commit ref):
```bash
br close <id> --reason "Added PKCE to OAuth login — commit a1b2c3d" --suggest-next --json
```
Never close with vague reasons like "Done" or "Implemented." Returns JSON with `closed`, `skipped`, and `unblocked` arrays showing newly unblocked work.

**Work is NOT done until `git push` succeeds:**
```bash
br sync --flush-only
git add .beads/
git commit -m "Close <id>: short summary"
git push
```

## Keep resumable notes

**`--notes` overwrites.** Always read existing notes before writing:
```bash
# Read existing
br show <id> --json | jq -r '.notes'

# Write combined old + new
br update <id> --notes "Previous content here...
---
COMPLETED: ... / IN_PROGRESS: ... / NEXT: ... / BLOCKERS: ..."
```

Use a consistent structure (COMPLETED / IN_PROGRESS / NEXT / BLOCKERS / KEY DECISIONS). Update at milestones, before handoffs, or when context is running low.

## Create quality issues

**Separate fields by purpose** — don't stuff everything into `-d`:
```bash
br create "Fix OAuth implicit grant → PKCE" -t task -p 1 \
  -d "Login flow uses deprecated implicit grant. Security risk per OAuth 2.1 spec."

br update <id> \
  --design "Replace implicit grant with PKCE in auth/oauth.go. Update callback handler." \
  --acceptance "Login uses PKCE. Implicit grant rejected. Existing sessions unaffected."
```

**Issues must be self-contained.** A fresh agent with zero session history must be able to work the issue from `br show` alone. Banned phrases in descriptions: "as discussed", "per the spec", "see above", "the issue from earlier."

**Titles must be specific.** "Fix auth" is not traceable; "Fix OAuth implicit grant → PKCE in login flow" is.

## Use core commands

| Action | Command |
|--------|---------|
| Create | `br create "Title" -t task -p 2 -d "Description"` |
| Create child | `br create "Subtask" --parent <epic-id>` (generates hierarchical ID like `br-abc.1`) |
| Quick capture | `br q "Quick note"` (creates placeholder, returns ID only) |
| Claim | `br update <id> --claim` |
| Update fields | `br update <id> --design "..." --acceptance "..." --notes "..."` |
| Close | `br close <id> --reason "..."` |
| Close multiple | `br close <id1> <id2> --reason "..."` |
| Show | `br show <id>` |
| List open | `br list --status open` |
| Ready work | `br ready` (unblocked, not deferred) |
| Blocked | `br blocked` |
| Search | `br search "term"` |
| Dependencies | `br dep add <child> <parent> --type parent-child` |
| Dep tree | `br dep tree <id>` |

Add `--json` for machine-readable output. Use `--format toon` for token-efficient output.

## Sync explicitly (critical difference from bd)

br **never** runs git commands. Sync is manual:

| When | Command |
|------|---------|
| Before commit/push | `br sync --flush-only` then `git add .beads/` |
| After pull | `br sync --import-only` |
| Check status | `br sync --status` |

Keep `.beads/issues.jsonl` under version control. Treat `.beads/beads.db` as local.

**Safety guards** (cannot be bypassed without `--force`):
- Empty DB guard: won't export 0 issues over existing JSONL
- Stale DB guard: won't export if JSONL has issues DB doesn't
- Conflict markers: refuses import if JSONL has unresolved git conflicts

**History**: `br history list` shows backups; `br history restore <backup>` recovers.

## Configure prefixes and paths

```bash
br config --set id.prefix=myproj    # Set prefix
br config --get id.prefix           # Get value
br config --list                    # Show all resolved config
```

Config layers (highest to lowest): CLI flags → env → `.beads/config.yaml` → `~/.config/beads/config.yaml`

Environment variables:
- `BEADS_DIR` — override `.beads/` location
- `BEADS_JSONL` — override JSONL path (requires `--allow-external-jsonl`)
- `BD_ACTOR` — default actor name for audit trail
- `RUST_LOG` — logging level

## Migrate from bd to br

1. Copy existing `.beads/issues.jsonl` into the repo if needed.
2. Run `br sync --import-only` to populate the br DB.
3. Verify with `br list --status open` or `br ready`.
4. Update docs/instructions to replace `bd` with `br` and use explicit sync.
5. Preserve existing `bd-*` IDs; new issues use configured prefix.

## Validate with lint

```bash
br lint                  # Check open issues for missing sections
br lint --type feature   # Filter by type
br lint --json           # Machine-readable output
```

Wire into pre-push hooks to catch issues that slipped through.

## Troubleshoot quickly

| Problem | Solution |
|---------|----------|
| Config wrong | `br config --list` to see resolved values |
| Unknown issue ID | `br list` or `br search "term"` |
| JSONL/DB out of sync | `br sync --status` then `--import-only` or `--flush-only` |
| "Refusing to export" | Safety guard triggered; check message, use `--force` only if certain |
| Blocked issue won't claim | Check `br dep tree <id>` for blockers, close them first |
