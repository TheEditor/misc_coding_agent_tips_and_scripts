# Misc Coding Agent Tips and Scripts

Guides for using AI coding agents *effectively*—not just getting tools installed, but understanding how to use them well.

## Philosophy

Most agent documentation answers "how do I set this up?" This repo answers "how do I use it thoughtfully?"

The difference matters. An agent can learn commands from `--help` but can't infer *why* certain patterns exist or when to apply them. These docs fill that gap.

## Contents

### Beads Issue Tracking

- [Issue Design Philosophy](beads/ISSUE_DESIGN.md) - Why fields exist and how to use them
- [Field Semantics](beads/FIELD_SEMANTICS.md) - Description vs design vs acceptance
- [Examples](beads/EXAMPLES.md) - Before/after issue creation patterns

## Usage

Point your agent to the relevant document when starting a session. For example, in a Claude Code CLAUDE.md:

```markdown
## Issue Tracking

When using beads, follow the guidance in:
https://github.com/TheEditor/misc_coding_agent_tips_and_scripts/blob/main/beads/ISSUE_DESIGN.md
```

## Related Resources

- [Jeffrey Emanuel's misc_coding_agent_tips_and_scripts](https://github.com/Dicklesworthstone/misc_coding_agent_tips_and_scripts) - Setup/configuration focused
- [Beads repo](https://github.com/steveyegge/beads) - Official beads documentation

## License

MIT
