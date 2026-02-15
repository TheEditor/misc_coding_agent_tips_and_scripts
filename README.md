# Misc Coding Agent Tips and Scripts

Operational guidance for AI coding agents—not setup docs, but best practices for using tools *well*.

## Philosophy

Most agent documentation answers "how do I install this?" This repo answers "how do I use it thoughtfully?"

An agent can learn commands from `--help` but can't infer *why* certain patterns exist or when to apply them. These docs fill that gap.

## Contents

### [Beads-Rust Issue Tracking](agent-operational-guides/beads-rust/)

Operational guidance for `br` (beads_rust). Covers issue design, field semantics, and agent workflows.

| Document | Purpose |
|----------|---------|
| [README](agent-operational-guides/beads-rust/README.md) | Operational differences, hierarchical IDs, claim workflow |
| [Issue Design](agent-operational-guides/beads-rust/ISSUE_DESIGN.md) | Why fields exist and how to use them |
| [Field Semantics](agent-operational-guides/beads-rust/FIELD_SEMANTICS.md) | Description vs design vs acceptance vs notes |
| [When to Create Issues](agent-operational-guides/beads-rust/WHEN_TO_CREATE_ISSUES.md) | Recognizing and filing discovered work |
| [Issues vs Planning Docs](agent-operational-guides/beads-rust/ISSUES_VS_PLANNING_DOCS.md) | Why issues should be self-contained |
| [Code in Issues](agent-operational-guides/beads-rust/CODE_IN_ISSUES.md) | When to include code snippets |
| [Examples](agent-operational-guides/beads-rust/EXAMPLES.md) | Before/after issue creation patterns |
| [Safety](agent-operational-guides/beads-rust/SAFETY.md) | Sync guards, history, data protection |

## Usage

Reference these docs from your AGENTS.md or CLAUDE.md. Example:

```markdown
## Issue Tracking

This project uses beads_rust (`br`). Follow the guidance at:
https://raw.githubusercontent.com/TheEditor/misc_coding_agent_tips_and_scripts/main/agent-operational-guides/beads-rust/README.md
```

Or fetch multiple docs for comprehensive context.

## Related Resources

- [Jeffrey Emanuel's misc_coding_agent_tips_and_scripts](https://github.com/Dicklesworthstone/misc_coding_agent_tips_and_scripts) - Setup/configuration focused
- [beads_rust repo](https://github.com/Dicklesworthstone/beads_rust) - Official br documentation and source

## License

MIT
