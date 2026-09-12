---
name: writing-plans
description: Use when you have a spec or requirements for substantial multi-step work that benefits from formal task decomposition, before touching code
---

# Writing Plans

## Overview

Write implementation plans that tell a skilled but codebase-unfamiliar developer what to build, where, and how to verify it. Focus on task boundaries, relevant files, interfaces, dependencies, acceptance criteria, and verification strategy. DRY. YAGNI. TDD where appropriate. Frequent commits.

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

A task is the smallest unit that carries its own test cycle and is worth a
fresh reviewer's gate. Fold setup, configuration, scaffolding, and
documentation steps into the task whose deliverable needs them; split only
where a reviewer could meaningfully reject one task while approving its
neighbor. Each task ends with an independently testable deliverable.

## Plan Document Header

**Every plan MUST start with this header:**

```markdown
# [Feature Name] Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

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

## No Placeholders

Every step must contain the actual content an engineer needs. These are **plan failures**:
- "TBD", "TODO", "implement later", "fill in details"
- "Add appropriate error handling" / "add validation" / "handle edge cases"
- "Write tests for the above" (without actual test code)
- "Similar to Task N" (repeat the code — the engineer may read tasks out of order)
- References to types, functions, or methods not defined in any task

## Self-Review

After writing the complete plan, check against the spec:

1. **Spec coverage:** Can you point to a task for each requirement? List gaps.
2. **Placeholder scan:** Search for red flags from the "No Placeholders" section.
3. **Type consistency:** Do types, method signatures, and property names match across tasks?

Fix issues inline.

## Execution Handoff

After saving the plan, choose the execution strategy based on subagent
availability, task independence, plan complexity, and coordination cost.

If subagents are available and tasks are mostly independent, use
superpowers:subagent-driven-development. Otherwise use
superpowers:executing-plans.

Ask the user which approach they prefer only if the choice materially
affects something they care about and they haven't already indicated a
preference. If the user already asked you to implement, proceed with the
best-fit strategy.
