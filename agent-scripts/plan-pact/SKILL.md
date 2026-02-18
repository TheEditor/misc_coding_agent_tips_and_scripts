---
name: plan-pact
description: Canonical cross-agent protocol for plan documents (storage, naming, YAML schema, dispute tracking, and archive-after-commit workflow).
---

# Plan Pact Skill

## Purpose

`plan-pact` is a negotiation harness for reasoning agents (human or software) to create, review, dispute, and close planning artifacts without filename drift, location drift, or process thrash.

Use the Decision Register as the current negotiated position and the Decision Log as negotiation history.

This skill is fully self-contained. Treat this file as the canonical protocol.

## Activation Policy

- Starting a new plan-pact cycle requires explicit end-user invocation.
- If planning intent is obvious but invocation is absent, ask for explicit activation confirmation before using plan-pact.
- Exception: when editing an already-active `.plan.md`, proceed without an extra invocation prompt.

## Progressive Disclosure

Read only what you need:

- Creating a new plan/ADR/critique: read `TL;DR`, `Storage + Naming`, `YAML Schema`, `Required Sections`, `Workflow`.
- Updating an existing plan: read `TL;DR`, `Workflow`, `Review Protocol`, `Dispute Tracking`.
- Handling disagreements: read `Dispute Tracking`.
- Closing work and archiving: read `Archive-After-Commit`.

## Non-Regression Guardrails

Do not degrade these cycle-1 strengths:

- Decision Register + append-only Decision Log.
- Required YAML frontmatter.
- Canonical storage and naming.
- Beads boundary.

## Scope Boundary

- Estimate calculation mechanics are out of scope for plan-pact.
- If effort/time computation is needed, defer that to downstream issue-tracking or implementation tooling.

## TL;DR

1. Active docs go in `plans/`; archived docs go in `plans/archive/`.
2. Use dual extension: `plans/YYYY-MM-DD-<topic-slug>.<type>.md`.
3. Allowed types: `plan`, `adr`, `critique`.
4. `spec` is an alias of `plan` (canonical type/filename remains `.plan.md`).
5. YAML frontmatter is required.
6. Edit-in-place is default for active plans.
7. `.plan.md` requires `Inspiration`, `Decision Register`, and append-only `Decision Log`.
8. `OPEN` is default; if `Flip Count >= 2`, set `DISPUTED`; only human resolution can set `LOCKED`.
9. Decision Log entries require actor, decision ID, change kind, and rationale.
10. The actor/tool that makes implementation commit must archive the plan file when appropriate.

## Storage + Naming

Canonical storage:
- Active artifacts: `plans/`
- Archived artifacts: `plans/archive/`
- Do not store canonical plan artifacts in repo root.
- `history/` is non-canonical session notes.

Filename pattern:
- `plans/YYYY-MM-DD-<topic-slug>.<type>.md`

Examples:
- `plans/2026-02-16-board-creation-ux.plan.md`
- `plans/2026-02-16-header-ownership.adr.md`
- `plans/2026-02-16-board-creation-ux.critique.md`

Naming rules:
- `<topic-slug>` is lowercase kebab-case.
- Do not include agent names in filenames.
- Do not use `-v2`/`-v3`; use supersession fields instead.

## YAML Schema (Required)

Every plan artifact must begin with YAML frontmatter:

```yaml
---
title: Board Creation UX
type: plan
date: 2026-02-16
topic: board-creation-ux
status: draft
authors:
  - codex
project: agwakwagan
supersedes: null
superseded_by: null
reviewed_by: []
---
```

Required keys:
- `title` (string)
- `type` (`plan` | `adr` | `critique`; incoming `spec` normalizes to `plan`)
- `date` (`YYYY-MM-DD`)
- `topic` (kebab-case string)
- `status` (`draft` | `active` | `blocked` | `complete` | `superseded`)
- `authors` (non-empty list of strings)
- `project` (string)

Optional keys:
- `supersedes` (string path or `null`)
- `superseded_by` (string path or `null`)
- `reviewed_by` (list of strings)

## Required Sections by Type

`.plan.md` must contain:
- Inspiration
- Problem Summary
- Goals
- Decision / Approach
- Implementation Plan
- Acceptance Criteria
- Open Questions
- Decision Register
- Decision Log (append-only)

`.adr.md` must contain:
- Context
- Decision
- Rationale
- Consequences

`.critique.md` must contain:
- Findings
- Risks
- Recommendations

## Workflow

### Create New Artifact

1. Choose type: `plan`, `adr`, or `critique`.
2. Choose topic slug.
3. Create file with canonical name in `plans/`.
4. Add required YAML frontmatter.
5. Fill required body sections.
6. For `plan`, initialize empty `Decision Register` + `Decision Log`.

### Update Existing Active Plan

1. Edit the existing active `.plan.md` in place.
2. Update frontmatter `status` as appropriate (`draft`, `active`, `blocked`).
3. When changing tracked decisions, update register and append a log entry.

### Replace/Supersede Plan

Use only for materially different direction:

1. Create new plan file in `plans/`.
2. Set new file `supersedes` to old file path.
3. Update old file:
   - `status: superseded`
   - `superseded_by: <new-file-path>`
4. Keep both files until archive step.

### Critique Another Plan

1. Create `.critique.md` in `plans/`.
2. Record target file in body and/or `reviewed_by`.
3. Keep critique separate from the target plan file.

## Review Protocol

Reviewers should use this order:

1. Decision Register and Decision Log consistency.
2. Decision-to-task alignment.
3. Scope creep and non-goal violations.
4. Residual risk and accessibility implications.

Required review output format:

1. Numbered findings.
2. Each finding tagged as `substantive`, `cosmetic`, or `question`.

Submission rule:

- Reviewers submit findings.
- Edits are applied only by the plan author or after explicit human approval.

## Dispute Tracking

All `.plan.md` files must include:

Decision Register columns:
- `ID` (stable identifier, e.g. `SPEC-012`)
- `Current Choice`
- `Flip Count`
- `Status` (`OPEN` | `DISPUTED` | `LOCKED`)
- `Last Editor`
- `Last Updated`

Decision status semantics:
- `OPEN`: default working state; autonomous edits are allowed.
- `DISPUTED`: flip threshold reached; autonomous edits are frozen pending human decision.
- `LOCKED`: set only after human resolution of a disputed decision.

Decision Log format (append-only):
- `changed|removed|superseded`: `YYYY-MM-DD <editor> <ID>: <change-kind> <old> -> <new> (reason: <reason>)`
- `confirmed`: `YYYY-MM-DD <editor> <ID>: confirmed <choice> (reason: <reason>)`

Allowed `change-kind` values:
- `changed`
- `confirmed`
- `removed`
- `superseded`

Rules:
1. A flip occurs when a decision returns to a prior choice (`A -> B -> A`).
2. Any change to a tracked decision appends a Decision Log entry.
3. If `Flip Count >= 2`, set `Status = DISPUTED` and freeze autonomous edits for that decision.
4. `DISPUTED` requires human resolution.
5. After human resolution, set `Status = LOCKED`.
6. No direct `OPEN -> LOCKED` transition is allowed.
7. `LOCKED` decisions reopen only with new evidence or changed requirements.
8. No-op changes are invalid (`A -> A` is not allowed).
9. Decision removals/merges require explicit rationale in the log entry.

## Archive-After-Commit

The actor/tool performing implementation commit is responsible for archive movement:

1. Complete implementation commit(s).
2. Move finished/superseded plan files from `plans/` to `plans/archive/`.
3. Update frontmatter status/links (`complete` or `superseded`, `supersedes`, `superseded_by`).
4. Commit the archive move as follow-up commit by the same actor/tool.

## Handoff Block

When handing off between agents, include:
- `Active Plan: <absolute-path>`
- `Status: <frontmatter-status>`
- `Next Decision Needed: <short text>`

## Beads Boundary

- Plan artifacts do not include beads commands or issue IDs unless explicitly requested.
- Convert approved plan outcomes into beads work in a separate step.

## Templates

- `templates/plan-template.plan.md`
- `templates/adr-template.adr.md`
- `templates/critique-template.critique.md`
