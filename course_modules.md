# The Course

We will build a terminal card game with AI assistance.
The objective is not just to ship a working game, but to observe common failure modes in AI-assisted development and understand how to respond to them.

---

## 1. First Experiment

It is widely understood that fully naive “vibe coding” is unreliable.
Instead, we will start with a structured workflow using a well-known specification toolkit such as:

- `obra/superpowers`
- `spec-kit`
- `openspec`
- or equivalent plugins

Commit `f442dbfdd18ab7e98c333ad1d29d82cab2d02f23` contains only a rules document.
This is our starting point.

---

## Step 1 — Initialize the Workflow

Start a new session or workspace using your chosen plugin.

For example, with `superpowers`:

```
/using-superpowers
/superpowers:brainstorm
```

Provide the following requirement:

```
Let’s build a terminal-based version of Regicide using the rules in @Regicide_Rules.md.

It should feel polished and intuitive to play, not just a basic CLI prototype.

Use Python. Structure it cleanly so we can extend it later.
```

This prompt intentionally:

- Specifies the language
- References a rule document
- Signals UX expectations
- Mentions extensibility
- Avoids specifying framework, control scheme, testing strategy, or tooling

We are deliberately leaving those decisions to the agent.

---

## Step 2 — Framework Selection

If the agent asks you to choose a TUI framework, accept its recommendation.

Given the requirements, we expect a strong agent to recommend **Textual** (optionally enhanced with `rich`).

If another framework is proposed, do not reject it immediately.
Instead, examine the reasoning carefully:

- Does it justify the choice relative to “polished” and “intuitive”?
- Does it consider event-driven interaction?
- Does it address extensibility?

We are observing reasoning quality, not enforcing a specific answer.

---

## Step 3 — Control Scheme

If asked about interaction preferences:

Choose:

- Arrow-key navigation
- Enter/space to confirm
- Optional keyboard shortcuts if suggested

If the agent does not ask about control preferences, observe how it resolves the ambiguity:

- Does it explicitly state its chosen control model?
- Does it default to numbered input?
- Does it document assumptions?

The phrase “polished and intuitive” is intentionally vague.
We are testing how the agent handles underspecified UX constraints.

---

## Step 4 — Planning and Execution

Follow the standard workflow of your plugin:

- Planning phase
- Architecture proposal
- Execution phase

Do not add extra constraints unless the agent requests clarification.

---

## Step 5 — Environment Management

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

---

## What We Are Observing

While the agent is building the app, pay attention to:

- Does the agent detect underspecified UX requirements?
- How does it justify framework choice?
- Does it separate domain logic from UI?
- Does it propose testing unprompted?
- Does “extensible structure” translate into actual architecture?
- How does it manage dependencies?

We are not trying to force failure.
We are observing default behaviors under realistic constraints.

---

Continue through the normal workflow and record decisions, assumptions, and any deviations from expectation.

## Step 6 — Integration Check and First Play-through

Commit `933af10d3c3b0a598f5ff47b4655f8e15b9d9d60`

When the agent claims the project is complete, do not begin analysis immediately.
First, use the application as a player would.

### 1. Entry Point

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

### 2. First Play-through

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

### 3. Rule Violation Discovery

Once the game is playable, begin identifying rule violations.

For each violation, document:

- The rule as written
- The expected behavior
- The observed behavior
- Steps to reproduce (if applicable)

Make a git commit before attempting any fixes.

---

### 4. Vibe Debugging (Iterative Agent Fixes)

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
agent’s iterative patch attempts.