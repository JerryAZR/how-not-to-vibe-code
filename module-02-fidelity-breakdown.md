# Module 2: Fidelity Breakdown Analysis

## Objective

In Module 1, we produced a playable but unreliable system. Now we analyze where
and why the implementation diverged from the specification. This module teaches
you to identify the specific failure modes that cause rule violations — not just
that they exist, but how they manifest in code.

---

## What We're Analyzing

We have two artifacts from Module 1:

1. **The commit before vibe debugging** — The original implementation produced
by the toolkit workflow
2. **Your documented rule violations** — The gaps you discovered between the
rules and runtime behavior

Now we examine the code to understand the root causes.

---

## Setup

Ensure you have:

- The git commit from Module 1 (pre-vibe-debugging)
- Your documented rule violations from Section 8.3
- The original Regicide rules document

### Bugs to Analyze

The following table summarizes bugs observed in our example project:

| # | Bug | Expected Behavior | Actual Behavior |
|---|-----|-------------------|------------------|
| BUG-1 | Missing enemy suit | Player can see enemy suit to plan moves with immunity in mind | Enemy shows only name, ATK, and health, no suit |
| BUG-2 | Cannot select cards to discard | Player selects which cards to discard during defense | Cards are automatically discarded |
| BUG-3 | Cannot flip Jester | Press 'j' to flip Jester, discard hand, draw 8 new cards | System warns "No Jester in hand" |
| BUG-4 | Diamond power not activating | Draw cards equal to rank when playing diamond (max 8) | No draws occur |
| BUG-5 | Heart power not activating | Playing heart recycles discard pile back to deck | Neither discard nor deck changes |
| BUG-6 | Played cards not in discard | Played cards added to discard when enemy defeated | Cards are lost forever |
| BUG-7 | No combo plays | Play multiple same-rank cards (≤10) or pet+card with combined suit powers | Enter plays single card and ends turn |

If you have your own bug list from a different Module 1 experiment, you are encouraged to analyze that instead. The methodology below applies to any set of rule violations.

## Analysis - Learn The Failure Patterns

### How to Read This Section

For each bug, we analyze:

* **What visibly failed**
* **How the code produced that failure**
* **What structural mistake enabled it**
* **How to detect similar failures in your own system**

Your goal is not to memorize these categories.

Your goal is to test whether your own project exhibits the same structural
patterns — even if the symptoms look different.

---

### BUG-1: Missing Enemy Suit

> Pattern: Model–View Drift

#### Surface Symptom

Enemy suit exists in rules and affects immunity logic, but is not displayed to
the player.

#### Code-Level Observation

* `Enemy` model contains `_immunity_suit`
* Combat logic uses this field
* `EnemyWidget` does not render it

The rule-relevant data exists.
The UI simply never exposes it.

#### Structural Root Cause

The system separated:

* **Rule state**
* **Player-visible state**

Without enforcing that rule-critical data must be observable.

The agent optimized for mechanical correctness, not informational completeness.

In interactive systems, **visibility is part of correctness**.

A rule that cannot be observed cannot meaningfully guide decisions.

#### Generalizable Diagnostic Questions

In your own project:

* Are there state fields used in logic that never appear in UI?
* Are there rule-relevant attributes that players must infer indirectly?
* If a player cannot see it, can they still make optimal decisions?

If yes, you likely have Model–View Drift.

---

### BUG-2: Cannot Select Cards to Discard

> Pattern: Agency Collapse

#### Surface Symptom

Player does not choose which cards to discard during defense.
System auto-discards using a greedy algorithm.

#### Code-Level Observation

* `handle_enemy_attack()` directly calls `discard_for_damage()`
* `discard_for_damage()` sorts hand and auto-removes cards
* No “Defense Phase”
* No input loop for discard selection

The architecture contains no representational space for player choice.

#### Structural Root Cause

The agent reframed:

> “Player discards cards equal to damage”

into:

> “System must compute a valid discard set”

It converted a decision problem into a deterministic algorithm.

This is common when the specification:

* Describes constraints
* But does not explicitly encode interaction phases

The model filled in missing procedural detail with automation.

#### Generalizable Diagnostic Questions

In your own system:

* Are there rule moments where a player should choose, but the system resolves automatically?
* Does your state machine explicitly encode decision phases?
* Is there any rule that says “player decides” but no code path that asks for input?

If so, check whether the model silently removed player agency.

---

### BUG-3: Cannot Flip Jester

> Pattern: Schema Overgeneralization

#### Surface Symptom

System checks for Jester in hand.
But Jesters are never created in the deck.

#### Code-Level Observation

* Flip logic assumes Jester originates in hand
* `create_tavern_deck()` never instantiates Jester cards

The agent implemented the action, but not the object lifecycle.

#### Structural Root Cause

The model imported a generic card-game schema:

> Playable cards originate from the main deck and enter hand.

But the rules describe Jesters as side resources.

The agent overrode specification details with genre priors.

This is not a reading failure — it is a pattern substitution failure.

#### Generalizable Diagnostic Questions

In your experiment:

* Does your system assume conventional mechanics not explicitly stated?
* Are there special-case entities treated like standard entities?
* Did the model “normalize” something unusual into a common pattern?

When your design deviates from common archetypes, LLMs frequently revert to the
archetype unless explicitly constrained.

---

### BUG-4 & BUG-5: Suit Powers Not Activating

> Pattern: Integration Fragmentation

#### Surface Symptom

Hearts and Diamonds power functions exist.
They are never invoked.

#### Code-Level Observation

* `resolve_hearts_power()` implemented
* `resolve_diamonds_power()` implemented
* `action_play_card()` never calls them

The logic exists in isolation.

#### Structural Root Cause

The model successfully completed subtasks independently, but never performed a
holistic integration pass.

This is a fragmentation failure:

* Rule logic implemented
* Action pipeline implemented
* Wiring between them missing

LLMs rarely perform cross-module integration unless explicitly instructed.

#### Generalizable Diagnostic Questions

In your project:

* Are there functions that look correct but are never called?
* Do you see “orphaned rule implementations”?
* Was there a final instruction to integrate all rule hooks?

If not, expect integration fragmentation.

---

### BUG-6: Played Cards Not in Discard

> Pattern: Lifecycle Fragmentation

#### Surface Symptom

Played cards are removed from hand but never stored.
They effectively disappear.

#### Code-Level Observation

* `hand.pop()` removes card
* No “table” container
* Later rule mentions discarding played cards after enemy defeat
* That lifecycle step was never encoded

The card’s state transitions were distributed across distant rule sections.

#### Structural Root Cause

The model recognized:

* Deck
* Hand
* Discard

But not:

* In-play / table zone

Because “table” was narratively described, not structurally defined.

Additionally, lifecycle rules were split across separate rule sections,
increasing the chance of local omission.

* **Step 1** says "Play a card from your hand onto the table in front of you ..."
* **Step 3** says "If the enemy is defeated, Place all cards played against the
enemy in the discard pile."

LLMs exhibit locality bias:
They strongly weight nearby text during generation.

#### Generalizable Diagnostic Questions

In your experiment:

* Does every entity have a clearly defined lifecycle?
* Is there a state container for every rule-described location?
* Are lifecycle transitions described far apart in the spec?

Distributed lifecycle definitions often produce missing state containers.

---

### BUG-7: No Combo Plays

> Pattern: Base-Rule Lock-In

#### Surface Symptom

Only one card can be played per turn.
No combo or companion logic exists.

#### Code-Level Observation

* `action_play_card()` handles exactly one card
* No multi-selection logic
* No aggregation abstraction

#### Structural Root Cause

The rules introduce:

> “Play a card…”

Later sections extend this to:

* Same-rank combos
* Pet + card plays
* Combined suit powers

The agent locked onto the first minimal definition and hardened it into architecture.

Later rule expansions were treated as descriptive rather than structural.

This is a failure to reopen an early abstraction.

#### Generalizable Diagnostic Questions

In your system:

* Did the agent implement only the simplest valid interpretation of an action?
* Are there rule expansions that were never folded back into the base mechanic?
* Does your input model restrict expressiveness compared to the rule space?

If yes, you may be seeing base-rule lock-in.

---

## Cross-Bug Synthesis: What Should You Test in Your Own Project?

After analyzing your own milestone, ask:

1. Did any player decisions get automated?
2. Are any rule functions implemented but unused?
3. Are there entities whose full lifecycle is not encoded?
4. Did early minimal definitions constrain later features?
5. Did genre assumptions override unusual rules?
6. Is all rule-relevant information visible to players?

If you cannot answer these questions confidently, investigate.

Do not assume your system is correct because it “mostly works.”

---

## Important: Do Not Accept This Framework Blindly

You should actively test whether:

* Your failures cluster into similar patterns.
* Or you are observing entirely different root causes.

It is possible your experiment reveals:

* State mutation ordering bugs
* Hidden coupling between subsystems
* Spec ambiguity amplification
* Concurrency drift
* Persistence inconsistencies

If so, extend the taxonomy.

Module 2 is not about proving this list is universal.

It is about training you to:

> Identify structural fidelity drift beneath surface bugs.

## Controlled Experiment: Neutral Comprehension Probe

### Purpose

Determine whether implementation failures stem from:

* Rule misunderstanding
  or
* Architectural drift during incremental construction

This experiment isolates comprehension from implementation.

---

### How to Phrase the Questions

Start a clean session with only the rule document.

Ask **neutral, structural questions** — not bug-directed ones.

Avoid:

* “Can players play multiple cards?”
* “Does the player choose which cards to discard?”

These steer the model.

Instead, ask:

* What phases exist in a full turn?
* What actions can the player take during each phase?
* How are enemy attacks resolved?
* Where do played cards go throughout their lifecycle?
* How are special cards introduced and used?

These questions:

* Do not imply something is wrong
* Force reconstruction of the full system
* Expose whether the action grammar, state transitions, and lifecycles are understood

This phrasing pattern generalizes to any project.

When auditing your own experiment, always ask:

* What are the phases?
* What are the player decisions?
* What are the state transitions?
* What is the lifecycle of each entity?

---

### Assumed Outcome

If the model reconstructs the system correctly — including combos, discard
choice, Jester handling, and card lifecycle — then rule comprehension is intact.

That is the expected outcome with sufficiently capable models.

---

### What This Teaches

If comprehension is correct but implementation was wrong, then:

The failure is not semantic misunderstanding.

The failure occurred during incremental code construction.

Specifically:

* Early abstractions hardened into architecture.
* Later rule refinements were not folded back into core mechanics.
* Global invariants were not re-evaluated after local changes.

This demonstrates a central property of vibe coding:

> LLMs can reason globally in analysis mode,
> but drift structurally during step-by-step generation.

The lesson is not about model intelligence.

It is about architectural persistence.

---

### Why This Matters

If rule violations were caused by misunderstanding, the solution would be:

* Better models
* Better parsing
* Better prompting

But if violations arise from architectural drift, the solution is:

* Explicit phase encoding
* Action grammar scaffolding
* Lifecycle modeling
* Integration checkpoints

This experiment distinguishes between those two classes of failure.

That distinction underpins the rest of the course.

---

## Module 2 Wrap-Up

In Module 1, we observed rule violations.

In Module 2, we traced those violations back to structural causes.

Across multiple bugs, one pattern emerged:

The model often understood the rules —
but lost fidelity during incremental construction.

* Abstractions hardened too early.
* Decision points disappeared.
* Lifecycles fragmented.
* Rule logic existed without integration.

The important shift is this:

> These were not random mistakes.
> They were predictable structural failure modes.

At this point, you should be able to:

* Look past surface bugs
* Identify where architecture drifted from specification
* Distinguish misunderstanding from construction failure

If you noticed questionable design patterns while tracing these bugs, that is
expected. We intentionally focused only on fidelity in this module.

In the next module, we will step back and examine the implementation itself —
not to fix specific bugs, but to evaluate the structural choices that made those
bugs likely.

Module 2 closes here:
You now know how to diagnose fidelity drift.
