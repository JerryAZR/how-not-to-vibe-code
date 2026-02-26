# How Not to Vibe Code

A hands-on course on understanding AI coding agent failure modes, how to detect them, and how to protect your codebase.

---

## What Is This?

This course teaches you to recognize and counteract the ways AI coding assistants produce code that "feels right" but doesn't work correctly. Using a terminal card game as the teaching vehicle, you'll build with AI assistance, then systematically observe where agents fail, why they fail, and how to respond.

## Why This Course?

Modern AI coding assistants are remarkably capable — they write code quickly, suggest solutions confidently, and can handle complex refactoring. But they also have systematic failure modes that are easy to miss until your codebase becomes unmaintainable.

This course gives you:

1. **Vocabulary** — Learn the names for patterns like "Agency Collapse" and "Integration Fragmentation"
2. **Diagnosis skills** — Identify failure modes before they compound
3. **Defense strategies** — Build checkpoints and validation gates into your workflow

---

## The Practice Project: Regicide TUI

The course uses **Regicide TUI** — a terminal-based implementation of the Regicide solo card game — as the teaching material.

### What is Regicide?

Regicide is a cooperative card game where you play cards to defeat 12 corrupt monarchs (Jacks, Queens, and Kings). You must deal enough damage to defeat each enemy before they strike back and destroy your hand.

### Why This Game?

Regicide is ideal because it has:

- **Complex state** — Multiple card zones (deck, hand, discard, in-play), turn phases, enemy progression
- **Rich rules** — Suit powers, combos, immunity, Jester mechanics
- **Clear success/failure** — You win or lose; no ambiguous states

This complexity creates many opportunities for fidelity drift.

### The Submodule

The practice project is included as a git submodule:

```
regicide-TUI/   # Terminal-based Regicide game
```

This project contains a **playable but flawed implementation** with 7 documented bugs that represent realistic vibe coding failures.

---

## Course Modules

### Module 1: The Toolkit Myth

**Status:** Complete

Demystify the "magic tool" narrative. Build Regicide using an AI toolkit workflow, then test runtime behavior against the official rules to see where fidelity breaks down.

**Key Lessons:**
- Toolkits improve structure but don't replace reasoning
- A system can feel correct while violating its specification
- Iterative patching can create the illusion of progress

### Module 2: Fidelity Breakdown Analysis

**Status:** Complete

Learn to identify where and why implementation diverged from specification. Analyze the 7 documented bugs to recognize structural failure patterns.

**Patterns Covered:**
- Model–View Drift
- Agency Collapse
- Schema Overgeneralization
- Integration Fragmentation
- Lifecycle Fragmentation
- Base-Rule Lock-In

### Module 3+

**Status:** Planned

---

## Prerequisites

- Basic Python knowledge
- Familiarity with terminal applications
- Access to an AI coding agent (Claude, Cursor, etc.)

## Recommended Toolkit

Any well-known AI assistant plugin or workflow (superpowers, spec-kit, etc.) — the point is to use *something*, then observe where it fails.

---

## Quick Start

### Option 1: Clone with Submodule (Recommended)

```bash
git clone --recurse-submodules https://github.com/JerryAZR/how-not-to-vibe-code.git
cd how-not-to-vibe-code
```

### Option 2: If You Forgot the Submodule

```bash
git clone https://github.com/JerryAZR/how-not-to-vibe-code.git
cd how-not-to-vibe-code
git submodule update --init --recursive
```

---

### After Cloning

1. Explore the course:
   - Read [SYLLABUS.md](SYLLABUS.md) for the full curriculum
   - Start with [module-01-toolkit-myth.md](module-01-toolkit-myth.md)

2. Try the practice project:
   ```bash
   cd regicide-TUI
   uv run regicide
   ```

---

## Contributing

This course is a living document. Each module is refined based on student
observations and emerging agent behaviors.
