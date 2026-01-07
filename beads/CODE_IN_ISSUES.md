# When to Include Code in Issues

> Guidance on putting code snippets in beads issues.

## The Decision

Ask yourself: **"Would a fresh agent waste significant time rediscovering this?"**

- Yes → Include the code
- No → Skip it

## When to Include Code

**Include when:**
- API integration with non-obvious query patterns
- Working code that took trial-and-error to get right
- "Occult" knowledge (undocumented behavior, quirks)
- Multi-session work where you'll lose context

**Skip when:**
- Standard patterns (CRUD, common libraries)
- Single-session work
- Code that's obvious from the description
- Simple bugs with clear fixes

## Which Field?

| Field | Code? | Why |
|-------|-------|-----|
| description | ❌ Never | Problem statement only, immutable |
| design | ✅ Yes | Implementation approach, can evolve |
| acceptance | ❌ Never | Success criteria, not implementation |
| notes | ✅ Yes | Session progress, discoveries |

**Rule**: Code goes in `design` (planned approach) or `notes` (discovered during work). Never in `description` or `acceptance`.

## What Good Looks Like

### ✅ Good: Working, Minimal, Contextual

```bash
bd update bd-42 --notes "WORKING CODE (tested):
\`\`\`python
service = build('drive', 'v3', credentials=creds)
result = service.about().get(fields='importFormats').execute()
# Returns dict with 49 entries like {'text/markdown': [...]}
\`\`\`
Note: text/markdown support added July 2024, not in official docs."
```

**Why it works:**
- Tested, not pseudocode
- Shows what it returns
- Explains non-obvious context

### ❌ Bad: Pseudocode

```bash
bd create "Add API integration" --design "Call the API and parse response"
```

**Problem:** No actual code. Future agent still has to figure out the details.

### ❌ Bad: Giant Dump

```bash
bd update bd-42 --notes "API response: {150 lines of JSON...}"
```

**Problem:** Too much noise. Extract the relevant structure.

### ❌ Bad: Obvious Code

```bash
bd create "Fix typo in config" --design "Open config.yaml, change 'teh' to 'the'"
```

**Problem:** Wastes tokens on obvious work.

## The Rediscovery Test

Before including code, ask:

1. **Did this take more than 5 minutes to figure out?** → Include it
2. **Is this in the official docs?** → Probably skip it
3. **Would I google this again next session?** → Include it
4. **Is this a standard pattern?** → Skip it

## Examples by Scenario

| Scenario | Include Code? | Reasoning |
|----------|---------------|-----------|
| Google API with undocumented field | Yes | Took research to discover |
| SQLite query with JOIN | No | Standard SQL |
| Regex that handles edge cases | Yes | Regexes are easy to get wrong |
| Simple HTTP GET | No | Obvious |
| Auth flow with refresh tokens | Yes | Token handling has gotchas |
| CRUD endpoint | No | Standard pattern |
| Workaround for library bug | Yes | Non-obvious, hard to rediscover |

## Summary

- **Include**: Working code that took effort to figure out
- **Skip**: Obvious stuff, standard patterns
- **Where**: `design` or `notes` field only
- **Format**: Minimal, tested, with context about why it matters
