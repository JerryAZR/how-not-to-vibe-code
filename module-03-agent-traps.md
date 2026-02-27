# Module 3: Agent Traps — What the Agent Leaves Behind

## Objective

Learn to recognize patterns and traps that agents leave in code — the specific
ways that AI-generated code tends to go wrong. This module focuses on two common
traps we've observed in this project.

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
- `engine/` contains helpers, not actual rule enforcement
- `ui/` contains complex logic, manipulating game states directly

**Why it happens**

The agent treats the UI callback as the natural place to put "what happens when
user does X". It doesn't think about separating "user did X" from "the game
rule that responds to X".

**Why it's a trap**

- UI becomes the de facto domain layer
- Testing requires running the UI
- Changes to rules risk breaking UI handling
- Hard to reason about game logic in isolation

**How to find it in this project**

Look in the UI files:

- Search for `action_play_card` or other event handlers (`action_*`)
- Check if game rules (attack power calculations, card validity checks) are inside these handlers
- Ask: is the rule logic in the handler, or does the handler call a domain function?

---

### Trap 2: Silent Defaults for Required Data

**What it looks like**

- Constructors accept optional parameters that should be required
- Missing data gets silently replaced with defaults in `__post_init__` or similar
- Type annotations say `X | None` when `None` is never valid

**Why it happens**

The agent doesn't want its code to crash, so it makes things "optional" to avoid
validation errors. It treats "can't fail" as more important than "must be correct".

**Why it's a trap**

- Constructor contracts become misleading
- Domain invariants are patched instead of enforced
- Bugs are hidden because invalid state auto-corrects
- Later code that relies on the field being meaningful gets surprises

**How to find it in this project**

Look in `src/models/enemy.py`:

- Find the `Enemy` class
- Observe that `_immunity_suit` accepts `None`
- Find where `__post_init__` silently replaces `None` with a default

This is a trap: the constructor accepts invalid state and patches it instead of
rejecting it.
