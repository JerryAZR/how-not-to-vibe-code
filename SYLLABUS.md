# Course Syllabus

A hands-on course on coding agent failure modes, how to detect them, and how to protect your codebase.

---

## Overview

This course uses a terminal card game (Regicide) as the teaching vehicle. Students build with AI assistance, then systematically observe where agents fail, why they fail, and how to respond.

---

## Modules

### Module 1: The Toolkit Myth

**Status:** Complete

**Objective:** Demystify the "magic tool" narrative. Many content creators claim specific toolkits, plugins, or workflows are all you need to reliably produce working software. This module lets students experience first-hand why those claims fall apart under realistic conditions.

**Key Lessons:**

- Toolkits improve structure and speed, but do not replace reasoning
- A system can feel correct while violating its specification
- Iterative patching can create the illusion of progress
- The "all you need" claim confuses scaffolding with solution

**Exercise Summary:**

1. Choose a well-known toolkit (superpowers, spec-kit, etc.)
2. Build Regicide following the toolkit's workflow
3. Test runtime behavior against the rules document
4. Attempt iterative bug fixes via the agent
5. Document where fidelity broke down

---

### Module 2: Fidelity Breakdown Analysis

**Status:** Complete

**Objective:** Analyze where and why the implementation diverged from the specification. Identify the specific failure modes that caused rule violations.

**Key Lessons:**

- Violations cluster into predictable categories (spec misinterpretation, incomplete implementation, state management, validation gaps)
- Most failures trace to missing validation
- Domain/UI boundary blurs in agent code
- Edge cases are consistently ignored

**Exercise Summary:**

1. Restore the original implementation (pre-vibe-debugging)
2. Categorize each rule violation by failure type
3. Trace violations to specific code locations
4. Identify systemic issues
5. Write a fidelity report

---

### Module 3: Code Review

**Status:** Complete

**Objective:** Read agent-produced code directly to identify patterns, smells, and structural issues that runtime testing won't reveal.

**Key Lessons:**

- Code looks better than it is — surface quality masks semantic errors
- Validation is consistently missing
- Domain logic is mixed with UI
- Magic numbers abound
- Tests are often decorative

**Exercise Summary:**

1. Read the architecture — understand structure
2. Find where game logic lives
3. Trace a rule violation to code
4. Identify code smells (architectural, logic, state)
5. Check edge case handling
6. Review tests (if they exist)
7. Write a code review summary

---

### Module 4: Test Authority Failures (Planned)

**Objective:** Observe how agents treat tests — as contractual invariants or as constraints to satisfy while minimizing friction.

---

### Module 5: Architectural Entropy (Planned)

**Objective:** Identify structural decay patterns that emerge in agent-produced code over time.

---

### Module 6: Detection and Protection (Planned)

**Objective:** Practical strategies for detecting failure modes early and protecting codebase integrity.

---

## Prerequisites

- Basic Python knowledge
- Familiarity with terminal applications
- Access to an AI coding agent (Claude, Cursor, etc.)

## Recommended Toolkit

- Any well-known AI assistant plugin or workflow (superpowers, spec-kit, etc.)

---

## Contributing

This course is a living document. Each module is refined based on student observations and emerging agent behaviors.
