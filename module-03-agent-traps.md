# Module 3: Agent Traps — What the Agent Leaves Behind

## Objective

Learn to recognize patterns and traps that agents leave in code — the specific ways that AI-generated code tends to go wrong. This module focuses on two common traps we've observed in this project.

---

## Setup

Checkout the project:

```
933af10d3c3b0a598f5ff47b4655f8e15b9d9d60
```

---

## The Traps

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

**How to find it in this project**

Look in the UI files:

- Search for `on_click` or event handlers
- Check if game rules (attack power calculations, card validity checks) are inside these handlers
- Ask: is the rule logic in the handler, or does the handler call a domain function?

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

**How to find it in this project**

Look in `engine/`:

- Find the `Enemy` class
- Check if `suit` accepts `None`
- Find where `__post_init__` silently replaces `None` with a default

```python
# This is what we see:
class Enemy:
    suit: Suit | None  # Should be required, not optional

    def __post_init__(self):
        if self.suit is None:
            self.suit = Suit.RED  # Silent fix instead of failing fast
```

This is a trap: the constructor accepts invalid state and patches it instead of rejecting it.

---

## Exercise

### Step 1 — Find Trap 1

Search for game rule enforcement in UI files:

- Open files in `ui/` or the main app file
- Look for `if` statements that implement game rules inside event handlers
- Document where you find business logic that should be in a domain layer

---

### Step 2 — Find Trap 2

Search for silent defaults in `engine/`:

- Find classes where `| None` is used on fields that should be required
- Find `__post_init__` methods that fix missing data instead of raising errors
- Document the fields that auto-correct

---

## What You Should Have Found

Based on this project, you likely found:

1. **Trap 1**: Rule enforcement in UI callbacks
2. **Trap 2**: `Enemy.suit` accepting `None`, auto-corrected in `__post_init__`

These aren't unique to this project. They're typical agent output.

---

## Next Steps

In Module 4, we will explore how agents handle tests — specifically, how they treat test failures and the authority of specifications.
