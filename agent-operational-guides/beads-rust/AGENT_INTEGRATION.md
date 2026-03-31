# Agent Integration

> How to deploy these best practices so agents actually follow them.

## The Problem

Documentation that agents read once at session start gets treated as suggestions. Quality standards need to be loaded as **constraints**, not reference material.

Think of it like the difference between a style guide on a shelf vs. a linter in CI. Both say the same thing; only one enforces it.

## The Three-Layer Model

```
Layer 1: Workflow context    → HOW to use br (commands, session protocol)
Layer 2: Quality constraints → WHAT standards apply (field requirements)
Layer 3: Validation          → WHEN to check (post-creation, pre-push)
```

### Layer 1: Workflow Context

The br SKILL.md or AGENTS.md (`br agents --add --force`) tells agents what commands exist and how to use them. Keep it focused on mechanics. Don't mix in quality rules here — agents treat session-start context as optional reference.

**Where it lives**: `.beads/AGENTS.md`, SKILL.md, or equivalent.

### Layer 2: Quality Constraints

Quality rules belong where your agent loads **hard constraints**:

| Agent | Location |
|-------|----------|
| Claude Code | `.claude/rules/beads-quality.md` |
| Cursor | `.cursorrules` |
| Codex, Copilot, Gemini CLI, Windsurf, Aider | `AGENTS.md` (read natively) |

**Why separate from Layer 1?** When workflow context and quality standards share a file, agents treat creation standards as optional reference rather than hard constraints. Separation makes constraints persistent.

### Layer 3: Validation

`br lint` checks for missing template sections by type. Wire it into your workflow:

```bash
# Manual check (defaults to open issues)
br lint

# Filter by type
br lint --type feature

# As a git pre-push hook
# In .git/hooks/pre-push or lefthook.yml:
br lint
```

Catches what slipped through Layers 1 and 2.

## Example Quality Rules

These are the constraints to put in Layer 2 (your agent's rules file):

```markdown
## Beads Quality Rules

### Creation Standards
- Descriptions must be self-contained. Banned phrases: "as discussed",
  "per the spec", "see above", "the issue from earlier", or any
  session-dependent reference. A fresh agent with zero context must be
  able to work the issue from `br show` alone.
- Always add `--design` for features and tasks (via `br update`).
- Always add `--acceptance` for bugs and features (via `br update`).
- Titles must be specific. "Fix auth" → "Fix OAuth implicit grant → PKCE
  in login flow."

### Close Discipline
- Close with `--reason` including commit ref and summary of what changed.
- Never close with vague reasons like "Done" or "Implemented."

### Notes Discipline
- `--notes` overwrites. Always read existing notes before updating.
- Include previous notes content when writing new notes.

### Dependency Wiring
- Link related issues with `br dep add`.
- When discovering bugs during feature work, create a bug issue and link
  it with `br dep add <bug> <original> --type discovered-from`.
```

## Verifying the Setup

After wiring in all three layers, test with a fresh agent session:

1. Does the agent run `br ready` or `br list` at session start? (Layer 1 working)
2. Does the agent create issues with separate description/design/acceptance? (Layer 2 working)
3. Does `br lint --status=open` pass after the agent creates issues? (Layer 3 working)

If Layer 2 fails, the usual cause is the rules file not being in the right location for your agent tool.
