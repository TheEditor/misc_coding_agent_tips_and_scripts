# Misc Coding Agent Tips and Scripts

Guides for using AI coding agents *effectively*—not just getting tools installed, but understanding how to use them well.

## Philosophy

Most agent documentation answers "how do I set this up?" This repo answers "how do I use it thoughtfully?"

The difference matters. An agent can learn commands from `--help` but can't infer *why* certain patterns exist or when to apply them. These docs fill that gap.

## Contents

### Beads Issue Tracking (Go `bd`)

Guidance for beads v0.35+. Tested against v0.43.

- [Issue Design Philosophy](beads/ISSUE_DESIGN.md) - Why fields exist and how to use them
- [Field Semantics](beads/FIELD_SEMANTICS.md) - Description vs design vs acceptance
- [When to Create Issues](beads/WHEN_TO_CREATE_ISSUES.md) - Recognizing and filing discovered work
- [Code in Issues](beads/CODE_IN_ISSUES.md) - When to include code snippets
- [Examples](beads/EXAMPLES.md) - Before/after issue creation patterns

### Beads-Rust Issue Tracking (Rust `br`)

Guidance for beads_rust (`br`). Assumes `id.prefix=br`.

- [Overview](beads-rust/README.md) - Operational differences and command reminders
- [Issue Design Philosophy](beads-rust/ISSUE_DESIGN.md) - Why fields exist and how to use them
- [Field Semantics](beads-rust/FIELD_SEMANTICS.md) - Description vs design vs acceptance
- [When to Create Issues](beads-rust/WHEN_TO_CREATE_ISSUES.md) - Recognizing and filing discovered work
- [Code in Issues](beads-rust/CODE_IN_ISSUES.md) - When to include code snippets
- [Examples](beads-rust/EXAMPLES.md) - Before/after issue creation patterns

## Usage

Point your agent to the relevant document when starting a session. For example, in a Claude Code CLAUDE.md:

```markdown
## Issue Tracking

When using beads, follow the guidance in:
https://github.com/TheEditor/misc_coding_agent_tips_and_scripts/blob/main/beads/ISSUE_DESIGN.md
```

For beads_rust:

```markdown
## Issue Tracking

When using beads_rust, follow the guidance in:
https://github.com/TheEditor/misc_coding_agent_tips_and_scripts/blob/main/beads-rust/ISSUE_DESIGN.md
```

## Beads Version Notes

These docs focus on single-agent workflows. They don't cover:
- Multi-agent swarm coordination (`bd swarm`)
- Formula system (`bd formula`, `bd cook`)
- Gate commands for async coordination (`bd gate`)
- Multi-rig routing

If you're using those features, consult the official beads documentation.

## Beads-Rust Notes

Beads-rust is leaner and non-invasive: `br` never runs git commands. Sync is explicit and you must `br sync --flush-only` before commit/push and `br sync --import-only` after pull. See [beads-rust/README.md](beads-rust/README.md) for operational details.

## Related Resources

- [Jeffrey Emanuel's misc_coding_agent_tips_and_scripts](https://github.com/Dicklesworthstone/misc_coding_agent_tips_and_scripts) - Setup/configuration focused
- [Beads repo](https://github.com/steveyegge/beads) - Official beads documentation
- [Beads-rust repo](https://github.com/Dicklesworthstone/beads_rust) - Official beads_rust documentation

## License

MIT
