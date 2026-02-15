# Issues Are the Work, Not the Planning Documents

> Guidance on the relationship between br issues and the planning artifacts that produced them.

## The Problem

Multi-agent workflows often produce planning documents before implementation begins: specs, architecture decisions, plan files, design reviews. These documents are valuable during planning but create a maintenance hazard if issues depend on them at execution time.

**Common failure mode**: Issues that say "See plan-v2.md for implementation details" or "Reference: architecture-decision-2026-02-14.md."

This creates two problems:

1. **Split source of truth.** The issue says one thing, the plan file says another (or says more). Which is authoritative? An agent encountering the issue must now read two documents and reconcile them.

2. **Maintenance drift.** Plan files are point-in-time artifacts. They reflect thinking at the moment of planning, not the current state of the work. If the design evolves during implementation (which it will — that's what the `--design` field is for), the plan file becomes stale. But the cross-reference implies it's still relevant.

## The Principle

**Issues are the work. Planning documents are the thinking that produced the work.**

Once an issue is populated with `description`, `design`, `acceptance_criteria`, and `notes`, it should be self-contained. An agent picking it up from `br ready` should be able to implement it without reading anything else.

If the issue isn't self-contained, the fix is to improve the issue — not to add a pointer to an external document.

## Why This Matters for Agents

### Compaction

When context compacts, the agent loses access to file contents unless they're re-read. An issue's fields survive compaction (they're summarized by br). A cross-reference to a plan file doesn't — the agent would need to know the file exists, find it, and read it. The cross-reference creates a hidden dependency on context that may not be available.

### Tool access

Not every agent execution context has filesystem access. An agent working through an API or MCP integration can call `br show` to get all issue fields. It may not be able to read arbitrary markdown files from the repo. Self-contained issues work in more contexts.

### Handoff

When work passes between agents (or between an agent and a human), the issue is the handoff artifact. If the issue says "see also: planning-doc.md," the receiving agent has to decide: is that document required reading or optional context? The ambiguity slows down the handoff.

## What Planning Documents Are For

Planning documents serve the *planning phase*:

- Exploring tradeoffs between approaches
- Getting human review and approval before implementation
- Coordinating between multiple agents or stakeholders
- Recording the reasoning behind decisions (ADRs)

Once planning concludes and issues are created, the planning documents become history. They belong in a `history/` directory (or equivalent) as archival artifacts, not as active references from live issues.

## The Distillation Step

The transition from planning to execution is a **distillation step**. The agent creating issues should synthesize the plan file's content into the issue's structured fields:

| Plan file content | Distills into |
|-------------------|---------------|
| Problem statement, motivation | `description` |
| Implementation approach, file targets | `design` |
| Success criteria, test cases | `acceptance_criteria` |
| Risks, constraints, open questions | `notes` |
| Time estimates | `estimated_minutes` |

After distillation, the issue contains everything needed. The plan file is the raw material; the issue is the refined product.

## When Cross-References Are Appropriate

There are cases where referencing external documents makes sense — but they're narrower than most agents assume:

- **ADRs (Architecture Decision Records)**: An issue implementing a decision may reference the ADR that explains *why* the decision was made. But the issue's `design` field should still contain the *how* — the ADR provides rationale, not implementation guidance.

- **External specifications**: If the work implements an external standard (an RFC, an API spec from a third party), referencing that spec is appropriate because it's a stable, versioned document the agent can't distill into the issue.

- **Large codebases with shared context**: A `CONTRIBUTING.md` or `ARCHITECTURE.md` that's actively maintained and applies to many issues may be worth referencing rather than duplicating.

Planning documents created by agents during the current workflow are none of these things. They're ephemeral, not versioned, and specific to the issues they produced.

## Summary

- Issues should be self-contained units of work.
- If an issue needs a plan file to be actionable, the issue is under-specified.
- The fix is to improve the issue's `design` / `acceptance` / `notes`, not to add a cross-reference.
- Planning documents are history after distillation. Archive them, don't link them.
