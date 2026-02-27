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

### Module 3: Agent Traps

**Status:** Complete

**Objective:** Learn to recognize characteristic patterns and traps that agents leave in code — specific failure modes that predictably appear in AI-generated code.

**Key Lessons:**

- Business logic living in UI callbacks
- Silent defaults for required domain data

**Exercise Summary:**

1. Find Trap 1: Business logic in UI
2. Find Trap 2: Silent defaults

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
