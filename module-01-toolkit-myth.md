# Module 1: The Toolkit Myth

## Objective

Demystify the "magic tool" narrative. Many content creators claim that specific AI toolkits, plugins, or workflows are all you need to reliably produce working software. This module lets you experience first-hand why that claim falls apart under realistic conditions.

---

## The Claim

You've likely seen content like:

- "Use `superpowers` and you'll never write buggy code again"
- "This one prompt pattern solves everything"
- "Just add this plugin and your agent becomes 10x better"
- "This workflow is production-ready"

These claims share a common structure: a specific tool is presented as transformative, success stories are shared (often with simple examples), and the implication is: just use this and you're covered.

---

## What We're Really Testing

Toolkits can help with structure, consistency, and the feeling of control. But they do not replace domain reasoning, specification integrity, or architectural judgment.

This module tests the gap between the promise and the reality.

---

## Setup

### Step 1 — Choose Your Toolkit

Select a well-known toolkit or plugin to evaluate. Popular options:

- `obra/superpowers` — Prompt scaffolding and workflow management
- `spec-kit` — Specification-focused development
- `openspec` — Open specification tooling
- Or any plugin you've seen recommended in a "this is all you need" video

Commit to one for this module. You will evaluate it honestly.

---

### Step 2 — Prepare the Task

Use the Regicide rules document as your specification source.

Commit `f442dbfdd18ab7e98c333ad1d29d82cab2d02f23` contains only the rules document — this is your starting point.

---

## Exercise

### Step 3 — Initialize the Toolkit Workflow

Start a new session using your chosen plugin exactly as recommended.

For example, with `superpowers`:

```
/using-superpowers
/superpowers:brainstorm
```

Follow the toolkit's exact prescribed steps. Do not skip ahead or modify the workflow.

---

### Step 4 — Execute the Requirement

Provide this requirement (or adapt for your toolkit):

```
Let's build a terminal-based version of Regicide using the rules in @Regicide_Rules.md.

It should feel polished and intuitive to play, not just a basic CLI prototype.

Use Python. Structure it cleanly so we can extend it later.
```

This requirement intentionally:

- Specifies the language
- References a rule document
- Signals UX expectations
- Mentions extensibility
- Avoids specifying framework, control scheme, testing strategy, or tooling

We are deliberately leaving those decisions to the agent.

---

### Step 5 — Document Every Assumption

As you work through the workflow, record in a notes file:

1. **What the toolkit prompted you to specify**
   - List each prompt or question the toolkit presented

2. **What it assumed automatically**
   - What decisions were made without asking you?

3. **Where you had to make decisions it didn't guide**
   - Where did the workflow become ambiguous?

4. **Where the workflow broke down**
   - What required manual intervention?

---

### Step 6 — Complete the Workflow

Follow through to completion:

- Planning phase
- Architecture proposal
- Execution phase

Do not add extra constraints unless the toolkit explicitly requests clarification.

---

### Step 7 — Environment Management

If the agent attempts to install dependencies globally using `pip install`, reject the action.

Require proper environment isolation:

- `uv`
- `venv`
- `poetry`
- `conda`
- or equivalent

We are observing whether the agent:

- Assumes global installation
- Recognizes best practices
- Proposes environment setup explicitly

Protect your system. Do not allow global installs.

---

### Step 8 — Integration Check and First Play-through

When the agent claims the project is complete, do not begin analysis immediately.
First, use the application as a player would.

#### 8.1 Entry Point

Check whether the project provides a clean and explicit entry point.

For example, `uv run regicide`, or an equivalent documented command.

If no clear entry point is provided:

- Ask the agent to define one.
- Observe whether it adjusts packaging, CLI configuration, or project structure.
- Note whether this process reveals additional issues.

A missing or awkward entry point may indicate that the agent optimized for:

- Passing tests
- Completing tasks in isolation
- Satisfying checklist items

rather than reasoning about end-user execution.

If preparing the entry point exposes new bugs, that is particularly informative.
It often signals a lack of integration-level validation.

---

#### 8.2 First Play-through

Once the entry point is ready, run the application and play the game.

At this stage, do not inspect the code.
You are observing runtime behavior only.

Questions to consider:

- Does the application start without crashing?
- Does the UI render consistently?
- Are controls responsive and predictable?
- Can the game progress through multiple turns?
- Are rules enforced as written?

With a moderately capable agent, the application will likely:

- Launch successfully
- Present a plausible UI
- Allow several initial moves

If you encounter blocking issues (crashes, soft-locks, missing UI elements),
work with the AI agent to resolve them until the game becomes playable end-to-end.

Do not analyze rule compliance yet.
The goal of this phase is system stability, not correctness.

---

#### 8.3 Rule Violation Discovery

Once the game is playable, begin identifying rule violations.

For each violation, document:

- The rule as written
- The expected behavior
- The observed behavior
- Steps to reproduce (if applicable)

Make a git commit before attempting any fixes.

---

#### 8.4 Vibe Debugging (Iterative Agent Fixes)

> This phase models real-world AI-assisted development, where users iteratively
> describe observed issues and accept patch proposals without inspecting
> architectural integrity.

Now engage the AI agent using the documented rule violations.

Provide the bug report and request fixes.

After the agent responds:

- Does it interpret the bug report correctly?
- Did the behavior change?
- Did it move in the correct direction?
- Did it introduce new bugs?
- Did it add or modify tests?

It is common for fixes to be partial or to introduce regressions.

It is also common for the agent to:

- Treat the existing implementation as the source of truth
- Claim the rule is ambiguous or incorrect
- Justify behavior that contradicts the written rules

You may iterate a few times, but do not over-optimize.
This phase is exploratory, not corrective.

---

Once experimentation is complete, rewind to the commit
prior to any rule-violation fixes.

We will analyze the original defects separately from the
agent's iterative patch attempts in the next module.

---

## Module 1 Wrap-Up: What Just Happened?

The toolkit worked — until it didn’t.

It generated a plausible application.
It launched.
It looked correct.
It allowed several moves.

But once we tested it against the actual rules, gaps appeared.

When we attempted iterative fixes, behavior shifted in unpredictable ways.
New issues appeared.
Some fixes addressed symptoms rather than causes.

The experience reveals something important:

Toolkits improve structure and speed.
They do not replace reasoning.

A system can feel correct while violating its specification.
An agent can confidently justify incorrect behavior.
Iterative patching can create the illusion of progress.

The "magic tool" narrative assumes that workflow replaces judgment.
This experiment demonstrates that it does not.

We now have a playable but unreliable system.

In the next module, we will step back and ask a harder question:

Where did the fidelity break down?
