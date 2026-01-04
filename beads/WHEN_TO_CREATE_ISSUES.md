# When to Create New Issues

> Guidance for agents on recognizing and filing discovered work.

## The Core Question

While working on issue `bd-X`, you encounter something unexpected. Should you:

1. Handle it now (scope creep risk)
2. Ignore it (lost context risk)
3. File a new issue (overhead, but preserves knowledge)

This document helps you decide.

## The Decision Framework

### Create an Issue When

**The work is real but out of scope:**
- You're implementing auth and notice the password hashing is weak
- You're fixing a bug and see another unrelated bug nearby
- You're adding a feature and realize a prerequisite is missing

**The work would survive compaction:**
- Would a future agent need this context?
- Is this something that matters beyond this session?
- Would forgetting this cause problems later?

**The work has dependencies:**
- It blocks or is blocked by other work
- Multiple issues relate to the same root cause
- It's part of a larger pattern you're noticing

### Don't Create an Issue When

**It's part of your current scope:**
- Edge cases you should handle anyway
- Tests for the feature you're building
- Documentation for what you're implementing

**It's trivial and immediate:**
- A typo you can fix in 10 seconds
- A missing import
- An obvious cleanup in code you're already touching

**It's speculative:**
- "We might need this someday"
- "This could be a problem if..."
- Premature optimization opportunities

## The `discovered-from` Pattern

When you create an issue while working on another, link them:

```bash
bd create "Weak password hashing in auth module" \
  -t bug -p 1 \
  -d "While implementing OAuth (bd-X), noticed password hashing uses MD5. Security risk." \
  --design "Replace MD5 with bcrypt in auth/password.go" \
  --acceptance "Passwords hashed with bcrypt. Existing MD5 hashes migrated on next login."

bd dep add <new-id> bd-X --type discovered-from
```

**Why link?**
- Creates audit trail (how was this found?)
- Helps future agents understand context
- Shows patterns (if bd-X keeps spawning discoveries, maybe bd-X is underspecified)

## Priority Heuristics

When filing discovered work, consider:

| Situation | Priority | Rationale |
|-----------|----------|-----------|
| Security issue | P0-P1 | Don't defer security |
| Blocks your current work | P1 | You can't finish without it |
| Would block future work | P2 | Important but not urgent |
| Nice-to-have improvement | P3 | Backlog material |
| Vague concern | Don't file | Clarify first or note in current issue |

## Type Selection

| What you found | Type |
|----------------|------|
| Something broken | `bug` |
| Missing capability needed | `feature` |
| Code quality / tech debt | `chore` |
| Larger scope than expected | `epic` (then decompose) |
| Investigation needed | `task` with clear acceptance criteria |

## Writing for Future Agents

The issue you create may be worked by:
- A different agent instance
- You, after compaction (effectively a different agent)
- A human reviewing the backlog

**Make the issue self-contained:**

```bash
# ❌ Bad - requires context from current session
bd create "Fix the thing we discussed" -t bug -p 2 \
  -d "The auth issue from earlier"

# ✓ Good - stands alone
bd create "Fix session token expiration check" -t bug -p 2 \
  -d "Session tokens are validated for format but not expiration. Found while implementing OAuth refresh (bd-X). Allows use of expired tokens until server restart." \
  --acceptance "Expired tokens rejected with 401. Token expiration checked on every authenticated request."
```

## The Compaction Test

Ask yourself: *If my context compacted right now and a fresh agent picked up this issue, would they understand what to do?*

If yes → the issue is well-written.
If no → add more context to description/acceptance.

## Continue or Block?

After filing a discovered issue, should you continue your current work or switch?

**Continue current work when:**
- Discovered issue doesn't block you
- You're close to completing current task
- Discovered issue is lower priority

**Consider switching when:**
- Discovered issue is P0 (security, data loss)
- Current work is blocked by the discovery
- Discovered issue is quick and you have full context now

**When uncertain:** File the issue with good context and continue. The issue preserves your knowledge; the priority system will surface it appropriately.

## Using `--suggest-next` [v0.37+]

When closing an issue, use `--suggest-next` to see what work just became unblocked:

```bash
bd close bd-42 --reason "Implemented" --suggest-next
```

This shows newly unblocked issues, helping you (or a future agent) find the natural next step without manually running `bd ready`.

## Anti-Patterns

### The Scope Creep Trap
*"I'll just fix this while I'm here..."*

Risk: Never finishing your actual task. Every detour spawns more detours.

Solution: File an issue, note the location, continue your work.

### The Lost Knowledge Trap
*"I'll remember this for later..."*

Risk: You won't. Context compacts. Sessions end. Knowledge evaporates.

Solution: If it's worth remembering, it's worth an issue.

### The Issue Flood
*"Everything is an issue!"*

Risk: Backlog becomes noise. Important work drowns in trivia.

Solution: Apply the "survives compaction" test. Trivial things don't need issues.

### The Vague Issue
*"Something's wrong with auth"*

Risk: Future agent can't act on it. Issue sits forever.

Solution: If you can't write clear acceptance criteria, you don't understand the problem yet. Investigate more or note it in your current issue's design/notes instead.

## Summary

| Signal | Action |
|--------|--------|
| Out of scope + real + worth remembering | Create issue with `discovered-from` link |
| In scope | Just do it |
| Trivial + immediate | Just do it |
| Speculative | Don't create issue |
| Can't define acceptance criteria | Investigate more first |
