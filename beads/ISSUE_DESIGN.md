# Beads Issue Design Philosophy

> This document teaches agents *how* to create good beads issues, not just *what* commands exist.

## The Problem

Agents learn beads commands from `bd prime` or `bd create --help`. But knowing the commands doesn't mean using them well.

**Common failure mode**: Stuffing everything into `--description`:

```bash
bd create "Add Sensitive field" -t task -p 1 \
  -d "Add Sensitive bool to SecretObject. Default true. Add json tag. Update constructor. Field controls default sensitivity."
```

This conflates *why* (the problem), *how* (the solution), and *done when* (success criteria) into one blob.

## The Fields

Beads provides distinct fields for distinct purposes:

| Field | Flag | Purpose | Changes? |
|-------|------|---------|----------|
| **title** | positional | What (short name) | Rarely |
| **description** | `-d` | Why (problem/context) | Never |
| **design** | `--design` | How (implementation approach) | Often |
| **acceptance** | `--acceptance` | Done when (success criteria) | Rarely |
| **notes** | (update only) | Progress log | Each session |

## Why This Matters

### 1. Compaction Survival

When context compacts, beads issues get summarized. If everything is in description, the summarizer can't distinguish problem from solution from criteria.

Well-structured issues survive compaction with meaning intact:
- Description → "Why did we create this?"
- Acceptance → "How do we know it's done?"

### 2. Design Evolution

Implementation approaches change as you learn. The `--design` field is explicitly mutable—update it as your understanding evolves without polluting the immutable problem statement.

### 3. Verification

Acceptance criteria in their own field can be checked mechanically. "Does the implementation satisfy each criterion?" becomes tractable.

## The Analogy

Think of it like a user story card:

- **Front of card** (visible, stable): Title + Description + Acceptance
- **Back of card** (working notes, evolving): Design + Notes

The front answers "what are we doing and why?" The back answers "how are we doing it?"

## When Creating Issues

Ask yourself:

1. **Title**: Can someone understand the work from 5 words?
2. **Description**: If I forgot everything else, would this explain *why* this matters?
3. **Design**: Is this *how* I'll solve it, separate from *what* I'm solving?
4. **Acceptance**: Could someone else verify completion from these criteria alone?

## See Also

- [Field Semantics](FIELD_SEMANTICS.md) - Detailed breakdown of each field
- [Examples](EXAMPLES.md) - Before/after patterns
