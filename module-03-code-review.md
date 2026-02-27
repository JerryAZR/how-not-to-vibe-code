# Module 3: Code Review — Reading What the Agent Actually Wrote

## Objective

In Module 1, we observed runtime behavior. In Module 2, we traced failures to code locations. Now we read the code directly. This module teaches you to conduct effective code review of agent-produced code — identifying patterns, smells, and structural issues that runtime testing alone won't reveal.

---

## Why Code Review Matters Here

When humans review code, they assume the author understood the problem. With agent-generated code, that assumption is unsafe:

- The agent may have implemented something plausible but wrong
- The code may work by accident on happy paths
- Architectural decisions may have been made without understanding trade-offs
- "It runs" doesn't mean "it's correct"

Code review is your primary defense against fidelity loss.

---

## Setup

Ensure you have:

- The git commit from Module 1 (the original implementation, pre-vibe-debugging)
- Your documented rule violations from Module 2
- Your Fidelity Report from Module 2

---

## Exercise

### Step 1 — Read the Architecture

Before diving into functions, understand the structure.

**Questions to answer:**

- How many files/modules exist?
- What is the directory structure?
- Where is the entry point?
- How is state managed?
- Where is game logic vs. UI code?

**What to look for:**

- Clear separation of concerns, or a single monolith?
- Evidence of planning, or ad-hoc organization?
- Meaningful file/module names, or generic ones?

---

### Step 2 — Find the Game Logic

Locate where the rules are implemented.

**Questions to answer:**

- Where are card values defined?
- Where is attack/defense calculated?
- Where are win/lose conditions checked?
- Where is state updated after each action?

**What to look for:**

- Rules implemented in one place, or scattered?
- Clear mapping from rules document to code?
- Magic numbers or hardcoded values?

---

### Step 3 — Trace a Rule to Code

Pick one rule violation from your Module 2 analysis and trace it to its implementation.

**For your chosen violation:**

1. **Find where the rule should be enforced** — Which function/module?
2. **Read that code carefully** — What does it actually do?
3. **Compare to the rule** — What's the difference?
4. **Identify the failure pattern** — Why did this happen?

**Common patterns you'll find:**

- **Wrong condition:** `if card.value >= enemy.health` instead of `if card.value == enemy.health`
- **Missing check:** No validation that player has enough attack power
- **Wrong variable:** Updating `player.health` when should update `enemy.health`
- **Simplified logic:** "All red cards deal damage" when rules are more complex

---

### Step 4 — Identify Code Smells

Read broadly and note anything that feels wrong. Don't fix yet — just identify.

**Architectural Smells:**

- Domain logic mixed with UI rendering
- Duplicate code handling the same concept
- Global state or singletons hiding dependencies
- Functions doing too many things

**Logic Smells:**

- Missing validation on inputs
- Empty catch blocks
- "Just in case" code that serves no purpose
- Comments describing what code does instead of why

**State Smells:**

- State stored in multiple places
- No clear source of truth
- Inconsistent update patterns
- Derived state recomputed instead of stored

---

### Step 5 — Check Edge Case Handling

Find where the code handles (or doesn't handle) boundary conditions.

**Questions to answer:**

- What happens when the deck runs out?
- What happens with an empty hand?
- What happens if the player enters invalid input?
- What happens with the last enemy?

**What to look for:**

- Explicit handling, or assume it won't happen?
- Error messages that actually help, or generic ones?
- Recovery paths, or just crashes?

---

### Step 6 — Review the Tests

If tests exist, read them. Don't run them yet.

**Questions to answer:**

- What do the tests validate?
- What's tested vs. what's not?
- Are edge cases covered?
- Do tests reflect the rules document?

**What to look for:**

- Tests that just create objects and check no errors
- Tests that only cover happy paths
- Tests that contradict each other
- Tests that pass but shouldn't (false positives)

---

### Step 7 — Write a Code Review Summary

Document your findings in a structured format.

**Code Review Report Template:**

```
## File: <filename>

### Overview
Brief description of what this file/module does.

### Findings

#### Issue Type: <Logic/Architecture/State/Validation>
**Location:** <function name, line number>
**Problem:** <what's wrong>
**Impact:** <how it affects behavior>
**Fix:** <brief suggestion>

...

### Overall Assessment
- Code quality: <good/acceptable/poor>
- Rule compliance: <high/partial/none>
- Maintainability: <high/medium/low>
```

---

## What You'll Observe

### 1. Code Looks Better Than It Is

Agent code is often:

- Properly formatted
- Reasonably organized
- Uses common patterns
- Looks "professional"

But surface quality masks semantic errors. The code runs but doesn't do what it should.

### 2. Validation Is Consistently Missing

Almost every violation traces to absent or weak validation:

- No checks on player inputs
- No assertions on state consistency
- No guard clauses for invalid transitions
- Trusting all external data

### 3. Domain Logic Is Mixed With UI

The agent tends to:

- Put game rules in event handlers
- Mix state updates with rendering
- Make UI changes that affect game mechanics
- Not separate "what happened" from "show user what happened"

### 4. Magic Numbers Abound

Rules that should be constants are hardcoded:

- Card values inline
- Damage multipliers inline
- No enum for card suits or ranks
- No configuration for game parameters

### 5. Tests Are Decorative

When present, tests typically:

- Create objects and call methods without assertions
- Only test that no exceptions are thrown
- Don't validate actual game behavior
- Pass because they test nothing meaningful

---

## Reflection Questions

After completing the code review:

1. Were you able to trace each violation to a specific code location?
2. Did any code look correct but actually contain subtle errors?
3. How much of the code was "infrastructure" vs. "game logic"?
4. Did you find issues that weren't caught by runtime testing?
5. What would you fix first? What's the highest-impact issue?

---

## The Real Lesson

Code review of agent-generated code requires different assumptions:

- **Assume nothing is correct** — Verify every claim
- **Read the rules alongside the code** — Compare constantly
- **Focus on boundaries** — Edge cases reveal the most
- **Trust the spec, not the code** — The rules document is authoritative
- **Look for what's missing** — Validation, error handling, tests

The agent writes code that looks plausible. Your job is to read what it actually does.

---

## Next Steps

In Module 4, we will explore detection and protection strategies — practical techniques for catching failures earlier and defending your codebase against agent-induced entropy.

---

## Optional: Compare With a Second Read

If possible, have another person (or your future self) review the same code without knowing the violations. Compare:

- Did they find the same issues?
- What did they notice that you missed?
- What did you notice that they missed?

This reveals blind spots in your review process.
