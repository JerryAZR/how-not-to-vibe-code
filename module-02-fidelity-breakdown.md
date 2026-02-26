# Module 2: Fidelity Breakdown Analysis

## Objective

In Module 1, we produced a playable but unreliable system. Now we analyze where and why the implementation diverged from the specification. This module teaches you to identify the specific failure modes that cause rule violations — not just that they exist, but how they manifest in code.

---

## What We're Analyzing

We have two artifacts from Module 1:

1. **The commit before vibe debugging** — The original implementation produced by the toolkit workflow
2. **Your documented rule violations** — The gaps you discovered between the rules and runtime behavior

Now we examine the code to understand the root causes.

---

## Setup

Ensure you have:

- The git commit from Module 1 (pre-vibe-debugging)
- Your documented rule violations from Section 8.3
- The original Regicide rules document

---

## Exercise

### Step 1 — Restore the Original State

If you haven't already, checkout the commit prior to any rule-violation fixes:

```bash
git log --oneline
# Find the commit before your vibe debugging attempts
git checkout <commit-hash>
```

Or identify the state you want to analyze.

---

### Step 2 — Categorize Each Violation

For each rule violation you documented, classify it by failure type:

**A. Specification Misinterpretation**

- The agent understood the rule incorrectly
- Implemented a plausible but wrong mechanic
- Example: "attack power = card value" instead of "attack power = suit-based formula"

**B. Incomplete Implementation**

- The rule was understood but not fully coded
- Edge cases were skipped
- Example: Basic play works, but special card effects are missing

**C. State Management Failure**

- The agent lost track of game state
- Variables were not updated correctly
- Example: Player health decrements incorrectly, or enemy AI state drifts

**D. Validation Gap**

- The agent didn't validate inputs or transitions
- Invalid moves were allowed
- Example: Playing a card when not enough attack power available

**E. UI-State Desynchronization**

- The displayed state doesn't match internal state
- Player sees one thing, game thinks another
- Example: UI shows enemy as "ready" but internally defeated

---

### Step 3 — Trace Each Failure to Code

For each violation:

1. **Find the relevant code** — Locate the function/module handling this rule
2. **Identify the decision point** — Where did the agent make the wrong choice?
3. **Analyze the logic** — Is the failure in:
   - Conditional logic (wrong condition)?
   - Data transformation (wrong formula)?
   - State update (missing/incorrect update)?
   - Missing validation (no check at all)?

4. **Document the pattern** — Does this failure match any known failure mode?

---

### Step 4 — Look for Underlying Causes

Beyond individual violations, examine the codebase for systemic issues:

**Architectural Red Flags:**

- Is domain logic mixed with UI code?
- Are rules implemented multiple times (duplication)?
- Is state scattered across different locations?
- Are there catch-all branches hiding invalid states?

**Testing Red Flags:**

- Are there any tests? Do they pass?
- Do tests validate rules or just object creation?
- Is there test coverage for edge cases?

**Spec Handling Red Flags:**

- Does the code reference the rules document?
- Are magic numbers/constants used without explanation?
- Is there any validation that inputs match expected rules?

---

### Step 5 — Summarize Findings

Create a "Fidelity Report" with:

1. **Violation Summary Table**

| Violation | Category | Location | Root Cause |
|-----------|----------|----------|------------|
| Enemy health doesn't reset | State Management | `game.py:142` | Missing state reset on new game |
| Special cards have no effect | Incomplete | `cards.py:89` | Effect logic not implemented |

2. **Systemic Issues**

- List any architectural or testing problems found across the codebase

3. **Root Cause Patterns**

- Which failure types appeared most frequently?
- Were there any surprising gaps?

---

## What You'll Observe

### 1. Spec Corruption Is Common

The most insidious failure mode: the code becomes the specification.

- The agent "forgets" the rules document
- Implementation details drift from written rules
- When questioned, the agent cites the code as authoritative

### 2. Validation Is Usually Missing

Most failures trace back to absent or inadequate validation:

- No checks on player inputs
- No assertions on state transitions
- No guard clauses for impossible states

### 3. Domain/UI Boundary Blur

The agent typically:

- Mixes game logic with rendering code
- Doesn't separate player decisions from state updates
- Makes UI changes that inadvertently affect game rules

### 4. Edge Cases Are Ignored

The "happy path" works. Everything else fails:

- First few turns work fine
- Later game states expose accumulated issues
- Special conditions (special cards, combo moves) are broken

---

## Reflection Questions

After completing the analysis:

1. Which failure type was most common in your implementation?
2. Could you trace each violation to a specific code location?
3. Did any violation surprise you — where the code looked correct but behaved wrongly?
4. Were there warning signs early in the build that predicted these failures?
5. How much of the failure was "agent didn't know" vs "agent forgot"?

---

## The Real Lesson

Fidelity breakdown is not random. It follows predictable patterns:

- **Validation is the first casualty** — Agents skip checks to "move faster"
- **Edge cases are ignored** — The happy path works, boundaries fail
- **Spec fades from view** — Code becomes the reference, not the document
- **State gets messy** — Updates are incomplete, inconsistent, or duplicated

These are not surprises. They are expected failure modes.

Knowing this lets you:
- Anticipate where failures will occur
- Add checkpoints before they compound
- Verify spec compliance proactively

---

## Next Steps

In Module 3, we will explore how agents handle tests — specifically, how they treat test failures, coverage, and the authority of specifications.

---

## Optional: Compare With Another Implementation

If you have access to a second Regicide implementation (or a student's work from another session), compare:

- Did the same failure types appear?
- Were failures in the same locations?
- Did different toolkits produce different failure patterns?

This reinforces that failures are systemic, not stochastic.
