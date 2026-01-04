# Beads Field Semantics

Detailed reference for each text field in `bd create`.

## title (required)

**What it is**: Short identifier for the work.

**Characteristics**:
- 5-10 words ideal
- Verb + noun pattern works well: "Add X", "Fix Y", "Refactor Z"
- Should be unique enough to distinguish from similar issues

**Examples**:
- ✓ "Add Sensitive boolean to SecretObject"
- ✓ "Fix credential rotation race condition"
- ✗ "Update the code" (too vague)
- ✗ "Implement the new feature for handling sensitive data in the secret object model with proper defaults" (too long)

---

## description (`-d`, `--description`)

**What it is**: The *problem statement*—why this work exists.

**Characteristics**:
- Immutable by design (problem doesn't change)
- Context for future-you after compaction
- Answers: "Why are we doing this?"

**Should contain**:
- What's wrong or missing
- Why it matters
- Relevant context/constraints

**Should NOT contain**:
- Implementation details (→ design)
- Success criteria (→ acceptance)
- Progress updates (→ notes)

**Example**:
```
SecretObject needs a Sensitive field to control default sensitivity 
for new fields during creation. Currently there's no way to mark an 
entire secret as sensitive by default, forcing field-by-field marking.
```

---

## design (`--design`)

**What it is**: The *implementation approach*—how you'll solve it.

**Characteristics**:
- Mutable (update as you learn)
- Technical details belong here
- Can be empty initially, filled in during work

**Should contain**:
- Technical approach
- Key implementation decisions
- File/function targets
- Dependencies or prerequisites

**Example**:
```
Add Sensitive bool to SecretObject struct in internal/model/secret.go.
Default true (fail-safe). Add json:",omitempty" tag so true values 
don't clutter JSON. Update NewSecretObject() constructor to set 
Sensitive: true.
```

---

## acceptance (`--acceptance`)

**What it is**: *Success criteria*—how you know it's done.

**Characteristics**:
- Stable (criteria shouldn't drift)
- Verifiable (can be checked)
- Answers: "How do we know this is complete?"

**Should contain**:
- Specific, testable conditions
- Observable behaviors
- Edge cases that must work

**Format options**:
- Bullet list of criteria
- Given/When/Then scenarios
- Simple declarative statements

**Example**:
```
- SecretObject has Sensitive bool field defaulting to true
- NewSecretObject() sets Sensitive: true
- JSON output omits Sensitive field when true (omitempty)
- Existing secrets without Sensitive field default to true on load
```

---

## notes (update only)

**What it is**: *Progress log*—session checkpoints and decisions.

**Characteristics**:
- Append-only in practice
- Updated via `bd update <id> --notes "..."`
- Survives compaction (summarized)

**Should contain**:
- Session progress
- Blockers encountered
- Decisions made and why
- Handoff context for next session

**Example**:
```
2024-12-27: Started implementation. Discovered Field.Sensitive already 
exists—this is for SecretObject-level default only. Updated design.
```

---

## estimate (`-e`, `--estimate`) [v0.29+]

**What it is**: Time estimate for the work.

**Characteristics**:
- Optional planning field
- Accepts minutes or human-readable format
- Useful for prioritization and capacity planning

**Format**:
```bash
bd create "Fix bug" -e 30           # 30 minutes
bd create "Add feature" -e 2h       # 2 hours
bd create "Refactor module" -e 1d   # 1 day
```

---

## Quick Reference

| Field | Flag | Mutable? | Survives Compaction? | One-liner |
|-------|------|----------|---------------------|-----------|
| title | positional | Rarely | Yes | What |
| description | `-d` | No | Yes (summarized) | Why |
| design | `--design` | Yes | Maybe | How |
| acceptance | `--acceptance` | Rarely | Yes | Done when |
| notes | (update) | Append | Yes (summarized) | Progress |
| estimate | `-e` | Yes | Yes | How long |
