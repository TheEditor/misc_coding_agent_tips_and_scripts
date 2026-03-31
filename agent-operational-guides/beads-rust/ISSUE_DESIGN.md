# Beads-Rust Issue Design Philosophy

> This document teaches agents *how* to create good beads_rust (br) issues, not just *what* commands exist.

## The Problem

Agents learn br commands from AGENTS.md (install with `br agents --add --force`) or `br create --help`. But knowing the commands doesn't mean using them well.

**Common failure mode**: Stuffing everything into `--description`:

```bash
br create "Add Sensitive field" --type task --priority 1 \
  -d "Add Sensitive bool to SecretObject. Default true. Add json tag. Update constructor. Field controls default sensitivity."
```

This conflates *why* (the problem), *how* (the solution), and *done when* (success criteria) into one blob.

## The Fields

Beads-rust provides distinct fields for distinct purposes:

| Field | Flag | Purpose | Changes? |
|-------|------|---------|----------|
| **title** | positional | What (short name) | Rarely |
| **description** | `-d` | Why (problem/context) | Never |
| **design** | `--design` | How (implementation approach) | Often |
| **acceptance** | `--acceptance` | Done when (success criteria) | Rarely |
| **notes** | (update only) | Progress log | Each session |

**Note:** `br create` sets title + description. Use `br update` to add or refine `design`, `acceptance`, and `notes`.

## Why This Matters

### 1. Compaction Survival

When context compacts, br issues get summarized. If everything is in description, the summarizer can't distinguish problem from solution from criteria.

Well-structured issues survive compaction with meaning intact:
- Description → "Why did we create this?"
- Acceptance → "How do we know it's done?"

### 2. Design Evolution

Implementation approaches change as you learn. The `--design` field is explicitly mutable—use `br update` to evolve it without polluting the immutable problem statement.

### 3. Verification

Acceptance criteria in their own field can be checked mechanically. "Does the implementation satisfy each criterion?" becomes tractable.

## The Analogy

Think of it like a user story card:

- **Front of card** (visible, stable): Title + Description + Acceptance
- **Back of card** (working notes, evolving): Design + Notes

The front answers "what are we doing and why?" The back answers "how are we doing it?"

## Banned Phrases in Descriptions

These phrases signal a non-self-contained issue. Never use them:

- "As discussed" / "as mentioned" / "per our conversation"
- "Per the spec" / "see the plan" / "reference: planning-doc.md"
- "The auth issue from earlier" / "the thing we talked about"
- "You know what I mean" / "the usual approach"

**The test**: Could a fresh agent, with zero session history, understand and implement this issue from `br show` alone? If any phrase requires shared context to parse, rewrite it with the actual information.

## When Creating Issues

Ask yourself:

1. **Title**: Can someone understand the work from 5 words?
2. **Description**: If I forgot everything else, would this explain *why* this matters?
3. **Design**: Is this *how* I'll solve it, separate from *what* I'm solving?
4. **Acceptance**: Could someone else verify completion from these criteria alone?

## See Also

- [Field Semantics](FIELD_SEMANTICS.md) - Detailed breakdown of each field
- [Examples](EXAMPLES.md) - Before/after patterns
