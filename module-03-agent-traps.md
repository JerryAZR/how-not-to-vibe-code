# Module 3: Agent Traps — What the Agent Leaves Behind

## Objective

Learn to recognize patterns and traps that agents leave in code — the specific ways that AI-generated code tends to go wrong. This isn't about general code review skills; it's about knowing what to look for when reviewing agent output.

---

## Why This Matters

Agent code has characteristic failure modes. These aren't random — they're predictable patterns that appear again and again. Once you know them, you can spot them in seconds instead of spending hours debugging.

This module trains your pattern recognition for agent-specific issues.

---

## Setup

Checkout the project:

```
933af10d3c3b0a598f5ff47b4655f8e15b9d9d60
```

---

## Common Agent Traps

Here are the patterns we'll look for. This isn't a complete list — agents find new ways to fail — but these cover most of what you'll encounter.

---

### Trap 1: Business Logic Living in UI

**What it looks like**

- Domain rules implemented directly inside UI event handlers
- State transitions mixed with button clicks or input processing
- `engine/` or `domain/` contains helpers, not actual rule enforcement

**Why it happens**

The agent treats the UI callback as the natural place to put "what happens when user does X". It doesn't think about separating "user did X" from "the game rule that responds to X".

**Why it's a trap**

- UI becomes the de facto domain layer
- Testing requires running the UI
- Changes to rules risk breaking UI handling
- Hard to reason about game logic in isolation

**How to spot it**

Look for:
- `if` statements inside click handlers that implement game rules
- State changes directly in UI callbacks
- No clear boundary between "user input" and "game decision"

---

### Trap 2: Silent Defaults for Required Data

**What it looks like**

- Constructors accept optional parameters that should be required
- Missing data gets silently replaced with defaults in `__post_init__` or similar
- Type annotations say `X | None` when `None` is never valid

**Why it happens**

The agent doesn't want its code to crash, so it makes things "optional" to avoid validation errors. It treats "can't fail" as more important than "must be correct".

**Why it's a trap**

- Constructor contracts become misleading
- Domain invariants are patched instead of enforced
- Bugs are hidden because invalid state auto-corrects
- Later code that relies on the field being meaningful gets surprises

**How to spot it**

Look for:
- `X | None` where `None` is never valid
- `default=` or `__post_init__` fixes on required fields
- Validation that fixes problems instead of raising errors

---

### Trap 3: The Happy Path Only

**What it looks like**

- Code only handles the expected case
- No validation of inputs
- No error handling
- Assumptions never checked

**Why it happens**

The agent writes code that "works" in the simple cases it considers. It doesn't think about edge cases, invalid inputs, or unexpected states.

**Why it's a trap**

- Edge cases fail silently or crash
- Invalid data propagates through the system
- Later code that receives bad data has no way to know it's invalid
- Security and stability issues

**How to spot it**

Look for:
- Functions with no input validation
- No type checking on external data
- `except:` blocks that catch everything
- Assert statements that are commented out or missing

---

### Trap 4: Implementation Becomes the Spec

**What it looks like**

- No reference to the rules document in code comments
- No links between rule definitions and implementations
- Code doesn't cite where rules came from

**Why it happens**

The agent "absorbs" the spec into the code and forgets the spec exists. The code becomes its own documentation.

**Why it's a trap**

- Impossible to verify code matches rules
- Later changes have no reference for what "correct" means
- Discrepancies between rules and code are invisible
- Agent starts citing code as authoritative when questioned

**How to spot it**

Look for:
- No comments referencing the spec
- Magic numbers without explanation
- Rules implemented but not documented in code
- When asked "where is X rule?", the answer is "in the code" not "in the rules doc"

---

### Trap 5: Premature Abstraction

**What it looks like**

- Interfaces for things that don't need interfaces
- Base classes with one subclass
- Extensibility points that are never extended
- "Strategy pattern" for simple if/else

**Why it happens**

The agent adds "extensibility" because it sounds good, without waiting for actual need. It prepares for a future that never comes.

**Why it's a trap**

- Extra code to maintain
- Indirection that makes debugging harder
- Abstraction that doesn't actually separate concerns
- "We might need this" that never happens

**How to spot it**

Look for:
- `class IFoo` with one implementation
- Abstract methods that only do one thing
- "Factory" for creating one type of object
- Extensibility that no extension points

---

## has Exercise

### Step 1 — Find Trap 1: Business Logic in UI

Search for game rule enforcement in UI files:

- Open files in `ui/`, `views/`, or the main app file
- Look for `if` statements that implement game rules
- Check event handlers — do they just pass data, or do they decide outcomes?

Document where you find it.

---

### Step 2 — Find Trap 2: Silent Defaults

Search for optional types on required fields:

- Look for `Enemy`, `Card`, `Player` classes
- Check `__init__` parameters for `None` defaults
- Find where `__post_init__` or validation "fixes" missing data

Document what should be required but isn't.

---

### Step 3 — Find Trap 3: No Validation

Look for functions that trust input:

- Card play functions — do they verify the play is legal?
- Attack functions — do they check attack power is sufficient?
- Player input — is invalid input handled?

Document places where happy path is assumed.

---

### Step 4 — Find Trap 4: Lost Spec

Check for spec references:

- Are there comments linking code to rules document?
- Do magic numbers have explanations?
- When you read the code, can you verify it matches the rules?

Document where the spec got lost.

---

### Step 5 — Find Trap 5: Premature Abstraction

Look for over-engineering:

- Are there interfaces or abstract classes with one use?
- Is there indirection that doesn't separate concerns?
- Did the agent add "extensibility" that isn't needed?

Document unnecessary complexity.

---

## What You Should Have Found

Based on this project, you likely found:

1. **Business Logic in UI** — Rule enforcement in event handlers
2. **Silent Defaults** — `Enemy.suit` accepting `None`, auto-corrected
3. **No Validation** — Plays accepted without checking attack power
4. **Lost Spec** — No references to rules document
5. **Premature Abstraction** — Any interfaces that aren't needed?

These aren't unique to this project. They're typical agent output.

---

## The Pattern

Agent code tends to:

1. **Put logic where it's easiest to write** — not where it belongs
2. **Avoid failures** — by making things optional or not checking
3. **Handle the simple case** — and ignore edge cases
4. **Forget the spec** — once it's in the code
5. **Add "extensibility"** — that never gets used

Knowing these patterns lets you spot them immediately.

---

## Next Steps

In Module 4, we will explore how agents handle tests — specifically, how they treat test failures and the authority of specifications.
