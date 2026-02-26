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

**Status:** Planned

**Objective:** Analyze where and why the implementation diverged from the specification. Identify the specific failure modes that caused rule violations.

**Expected Topics:**

- Spec corruption (code > spec)
- Missing validation paths
- State management failures
- Edge case handling

---

### Module 3: Test Authority Failures

**Status:** Planned

**Objective:** Observe how agents treat tests — as contractual invariants or as constraints to satisfy while minimizing friction.

**Expected Topics:**

- Dismissing red baselines
- Flaky test normalization
- Test relaxation
- Filler tests without semantic validation

---

### Module 4: Architectural Entropy

**Status:** Planned

**Objective:** Identify structural decay patterns that emerge in agent-produced code over time.

**Expected Topics:**

- Duplicate/shadow state
- Silent fallback routes masking invalid states
- Premature abstraction
- Patch stacking

---

### Module 5: Detection and Protection

**Status:** Planned

**Objective:** Practical strategies for detecting failure modes early and protecting codebase integrity.

**Expected Topics:**

- Red flags to watch for
- Checkpoints and validation gates
- When to override the agent
- Building a personal checklist

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
