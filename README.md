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

### [br-issue-tracking](agent-scripts/br-issue-tracking/)

Agent skill for tracking multi-session work with `br` (beads_rust). Covers session startup, claiming and closing issues, keeping resumable notes, syncing JSONL/DB, configuring prefixes, migrating from `bd`, and troubleshooting.

| Document | Purpose |
|----------|---------|
| [SKILL.md](agent-scripts/br-issue-tracking/SKILL.md) | Full skill: session workflow, core commands, sync protocol, config, migration, troubleshooting |

Key concepts: explicit sync (never automatic git), `--claim` for atomic assignment, resumable notes structure (COMPLETED / IN_PROGRESS / NEXT / BLOCKERS), safety guards against data loss.

### [Plan-Pact](agent-scripts/plan-pact/)

Cross-agent negotiation protocol for planning documents. Gives multiple reasoning agents (human or software) a shared format for proposing, reviewing, disputing, and closing plans without drift or rewrite loops.

| Document | Purpose |
|----------|----------|
| [SKILL.md](agent-scripts/plan-pact/SKILL.md) | Canonical protocol: storage, naming, frontmatter, dispute tracking, review protocol, activation policy |
| [Plan Template](agent-scripts/plan-pact/templates/plan-template.plan.md) | Starter template for `.plan.md` files with Inspiration section and decision log examples |
| [ADR Template](agent-scripts/plan-pact/templates/adr-template.adr.md) | Starter template for architecture decision records |
| [Critique Template](agent-scripts/plan-pact/templates/critique-template.critique.md) | Starter template for plan critiques |

Key concepts: Decision Register (current negotiated position), Decision Log (append-only negotiation history), Inspiration section (accountability anchor for every plan cycle), explicit activation policy (human invokes, agents don't self-activate).

## Usage

Reference these docs from your AGENTS.md or CLAUDE.md. Example:

```markdown
## Issue Tracking

This project uses beads_rust (`br`). Follow the guidance at:
https://raw.githubusercontent.com/TheEditor/misc_coding_agent_tips_and_scripts/main/agent-operational-guides/beads-rust/README.md
```

To use a skill, copy it into your agent's skill directory:

```bash
# br-issue-tracking (for Claude)
cp -r agent-scripts/br-issue-tracking/ .claude/skills/br-issue-tracking/

# plan-pact (for Claude)
cp -r agent-scripts/plan-pact/ .claude/skills/plan-pact/

# For Codex, use .codex/skills/ instead
```

Or fetch multiple docs for comprehensive context.

## Related Resources

- [Jeffrey Emanuel's misc_coding_agent_tips_and_scripts](https://github.com/Dicklesworthstone/misc_coding_agent_tips_and_scripts) - Setup/configuration focused
- [beads_rust repo](https://github.com/Dicklesworthstone/beads_rust) - Official br documentation and source

## License

MIT
