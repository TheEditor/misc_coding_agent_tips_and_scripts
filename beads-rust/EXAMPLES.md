# Beads-Rust Issue Examples

Before/after patterns showing proper field separation.

**Note:** Examples assume `id.prefix=br`.

---

## Example 1: Adding a Model Field

### ❌ Before (everything in description)

```bash
br create "Add Sensitive boolean to SecretObject model" --type task --priority 1 \
  -d "Add Sensitive bool field to SecretObject struct in internal/model/secret.go. Default value must be true (fail-safe). Add json tag with omitempty so true values do not clutter JSON output. Update NewSecretObject constructor to set Sensitive: true. This field controls the DEFAULT sensitivity for new fields during creation. Actual display uses Field.Sensitive."
```

**Problems**:
- Implementation details mixed with rationale
- No clear success criteria
- After compaction, hard to distinguish "why" from "how"

### ✓ After (proper separation)

```bash
br create "Add Sensitive boolean to SecretObject model" \
  --type task --priority 1 \
  -d "SecretObject needs a Sensitive field to control default sensitivity for new fields during creation. Currently no way to mark an entire secret as sensitive by default."

# Assume new issue ID is br-123
br update br-123 \
  --design "Add Sensitive bool to SecretObject struct in internal/model/secret.go. Default true (fail-safe). Add json:\",omitempty\" tag. Update NewSecretObject() constructor." \
  --acceptance "SecretObject has Sensitive bool defaulting to true. NewSecretObject() sets it. JSON omits field when true."
```

---

## Example 2: Bug Fix

### ❌ Before

```bash
br create "Fix race condition" --type bug --priority 0 \
  -d "There's a race condition when rotating credentials. Two goroutines can read the old credential simultaneously, both decide to rotate, and one overwrites the other's rotation. Need to add mutex or use atomic compare-and-swap. Should also add a test that spawns 100 goroutines to verify fix works."
```

### ✓ After

```bash
br create "Fix credential rotation race condition" \
  --type bug --priority 0 \
  -d "Two goroutines can read stale credential simultaneously, both trigger rotation, one overwrites the other. Results in credential loss under concurrent load."

# Assume new issue ID is br-124
br update br-124 \
  --design "Add sync.Mutex around credential read-check-rotate sequence in rotator.go. Consider RWMutex if read contention becomes issue." \
  --acceptance "No credential loss under 100 concurrent rotation attempts. Race detector passes. Existing rotation tests still pass."
```

---

## Example 3: Epic with Children

### Parent Epic

```bash
br create "Authentication System" \
  --type epic --priority 1 \
  -d "Users need to authenticate before accessing secrets. Currently the API is open to anyone with network access."

# Assume new epic ID is br-a1b2
br update br-a1b2 \
  --acceptance "All secret endpoints require valid JWT. Invalid/expired tokens return 401. Tokens issued via /auth/login endpoint."
```

### Child Tasks

```bash
# Create first child task
br create "Design JWT token structure" \
  --type task --priority 1 \
  -d "Need to define what claims go in the JWT and token lifetime."

# Assume new task ID is br-c3d4
br update br-c3d4 \
  --design "Use standard claims (sub, iat, exp). Add custom 'permissions' claim array. 1 hour lifetime, refresh via separate endpoint." \
  --acceptance "JWT structure documented. Sample token can be generated and validated."

# Link to parent epic
br dep add br-a1b2 br-c3d4 --type parent-child

# Create second child task
br create "Implement /auth/login endpoint" \
  --type task --priority 1 \
  -d "Users need an endpoint to exchange credentials for JWT."

# Assume new task ID is br-e5f6
br update br-e5f6 \
  --design "POST /auth/login accepts {username, password}. Validate against user store. Return {token, expires_at} on success." \
  --acceptance "Valid credentials return 200 with JWT. Invalid credentials return 401. Endpoint documented in OpenAPI spec."

# Link to parent epic
br dep add br-a1b2 br-e5f6 --type parent-child
```

**Note**: Dependencies are added after issue creation using `br dep add`. Use `--type parent-child` to explicitly mark the epic/subtask relationship.

---

## Example 4: Refactoring

### ❌ Before

```bash
br create "Refactor storage layer" --type task --priority 2 \
  -d "The storage layer is messy. Extract interface, add better error handling, maybe add caching later."
```

**Problems**:
- Vague scope ("messy")
- Mixed concerns (interface, errors, caching)
- No clear done state

### ✓ After

```bash
br create "Extract Storage interface from concrete implementation" \
  --type task --priority 2 \
  -d "Storage logic is tightly coupled to SQLite. Can't unit test handlers without real database. Need interface for mocking."

# Assume new issue ID is br-200
br update br-200 \
  --design "Define Storage interface in internal/storage/storage.go. Move SQLite implementation to internal/storage/sqlite/. Update all callers to use interface." \
  --acceptance "Storage interface exists. SQLite implements it. Handler tests use mock storage. No direct sqlite imports outside storage package."
```

---

## Pattern Summary

| Field | Ask Yourself |
|-------|--------------|
| title | "Can someone understand this in 5 words?" |
| description | "Why does this matter? What's broken/missing?" |
| design | "How will I implement this? What files/functions?" |
| acceptance | "How will I verify this is done?" |

---

## Anti-Patterns to Avoid

1. **The Wall of Text**: Everything in description
2. **The Empty Acceptance**: No way to verify completion
3. **The Premature Design**: Detailed design before understanding the problem
4. **The Vague Title**: "Fix bug" or "Update code"
5. **The Scope Creep**: Multiple unrelated concerns in one issue
