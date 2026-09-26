---
name: writing-plans
description: Use when you have a spec or requirements for substantial multi-step work that benefits from formal task decomposition, before touching code
---

# Writing Plans

## Overview

Write implementation plans that tell a skilled but codebase-unfamiliar developer what to build, where, and how to verify it. Focus on task boundaries, relevant files, interfaces, dependencies, acceptance criteria, and verification strategy. DRY. YAGNI. TDD where appropriate. Commits in logical chunks of related edits.

Include exact code when the API/signature/value is a requirement, ambiguity would be costly, or a tricky algorithm benefits from a concrete example. Otherwise describe the required behavior precisely enough for the implementer to write good code and tests.

**Announce at start:** "I'm using the writing-plans skill to create the implementation plan."

**Context:** If working in an isolated worktree, it should have been created via the `superpowers:using-git-worktrees` skill at execution time.

**Save plans to:** `docs/superpowers/plans/YYYY-MM-DD-<feature-name>.md`
- (User preferences for plan location override this default)

## Scope Check

If the spec covers multiple independent subsystems, suggest breaking this into separate plans — one per subsystem. Each plan should produce working, testable software on its own.

## File Structure

Before defining tasks, map out which files will be created or modified and what each one is responsible for.

- Design units with clear boundaries and well-defined interfaces. Each file should have one clear responsibility.
- Prefer smaller, focused files over large ones that do too much.
- In existing codebases, follow established patterns.

This structure informs the task decomposition.

## Task Right-Sizing

Divide by coherent behavior, not file boundaries. Each task has one clear
objective and no unrelated cleanup — record optional cleanup as a deferral
instead. Order tasks by genuine dependencies: a task that establishes
behavior, contracts, or data another task needs comes first, and the two
are not parallelized. Mark tasks parallel-safe only when each can be
implemented, tested, reviewed, and reverted independently.

A task is the smallest unit that carries its own test cycle and is worth a
fresh reviewer's gate. Fold setup, configuration, scaffolding, and
documentation steps into the task whose deliverable needs them; split only
where a reviewer could meaningfully reject one task while approving its
neighbor. Each task ends with an independently testable deliverable.

## Plan Document Header

**Every plan MUST start with this header:**

```markdown
# [Feature Name] Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

**Spec:** [path to the spec/design doc this plan implements]

## Global Constraints

[The spec's project-wide requirements — version floors, dependency limits,
naming and copy rules, platform requirements — one line each, with exact
values copied verbatim from the spec.]

---
```

## Task Structure

````markdown
### Task N: [Component Name]

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py`
- Test: `tests/exact/path/to/test.py`

**Interfaces:**
- Consumes: [what this task uses from earlier tasks — exact signatures]
- Produces: [what later tasks rely on — exact function names, parameter
  and return types]

**Requirements:**
[Describe what to build, important edge cases, and acceptance criteria.
Include exact code only when the API/value is a requirement or ambiguity
would be costly.]

**Tests:**
[Describe required test behavior: what cases to cover, expected outcomes.
Include exact test code when the test logic itself is a requirement;
otherwise describe the behavior precisely enough for the implementer.]

**Verification:**
Run: `pytest tests/path/test.py -v`
Expected: all tests pass
````

## Commits

A task is a unit of review; a commit is a unit of history. Commit related
edits together as one coherent change; don't accumulate a whole task into
one bulk commit. Each commit builds and passes its own tests. A schema
change, its backfill, and the code that reads it may be one commit; that
plus an unrelated rename is two. Messages say what changed and why.

## What a Task Contains

A task is ready when the implementer can build exactly one reasonable thing
from it: unambiguous, not complete. Each part carries what makes it
unambiguous and nothing more:

- **Requirements:** the behavior, the edge cases by name, the acceptance
  criteria, and every exact value the spec pins.
- **Interfaces:** exact signatures other tasks produce or consume.
- **Tests:** the cases and their expected outcomes. Test code only when the
  test logic itself is a requirement.
- **Verification:** the command to run and the output that means it passed.
- **A reference to another task:** point to that task's Interfaces block;
  don't repeat its content.

Two opposite failures:

- **Gaps** — lines that decide nothing: "TBD", "TODO", "fill in details",
  "handle edge cases" without naming them, "write tests" without saying
  which cases, a type or function no task defines.
- **Transcripts** — function bodies the signature and tests already
  determine. A plan longer than the code it describes has written the code
  instead.

Exact files and signatures belong in a plan the implementing agent writes
and executes itself. When you write a prompt for a *different* agent or
session outside this plan workflow, state the problem, desired outcome,
and acceptance criteria instead. Add investigation evidence, binding
constraints, non-goals, required verification, or known risks only when
they reduce risk for that task. Name files only as evidence; do not
mandate files to edit, new names, internal signatures, a step-by-step
procedure, or a new abstraction unless an existing contract or recorded
decision requires it — and cite that constraint. Before handing off, cut
any line that dictates an implementation merely because it was
convenient. Prefer the shortest prompt that gives the implementer what it
needs.

## Self-Review

After writing the complete plan, check against the spec:

1. **Spec coverage:** Can you point to a task for each requirement? List gaps.
2. **Gap and transcript scan:** Every task lets the implementer build exactly one reasonable thing, and carries no more than that (see "What a Task Contains").
3. **Type consistency:** Do types, method signatures, and property names match across tasks?
4. **Proportion:** A plan several times longer than its spec is a transcript. Replace bodies with signatures, test cases, and acceptance criteria.

Fix issues inline.

## Execution Handoff

After saving and self-reviewing the plan, link it for your human partner
and ask them to review it. Wait for their approval before implementation.
Include any open design questions the plan surfaced, each with your
recommendation.

Recommend an execution strategy in the same message, in one sentence:
superpowers:subagent-driven-development when subagents are available and
tasks are mostly independent (per-task reviews for non-trivial tasks, plus a final review), otherwise
superpowers:executing-plans (cheaper, one final review). If your human
partner already stated a preference, use it without asking again.
