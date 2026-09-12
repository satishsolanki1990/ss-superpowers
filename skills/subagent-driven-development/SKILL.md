---
name: subagent-driven-development
description: Use when executing implementation plans with independent tasks in the current session
---

# Subagent-Driven Development

Execute plan by dispatching a fresh implementer subagent per task, a task review after each, and a broad whole-branch review at the end.

**Why subagents:** You delegate tasks to specialized agents with isolated context. By precisely crafting their instructions and context, you ensure they stay focused and succeed at their task. They should never inherit your session's context or history — you construct exactly what they need. This also preserves your own context for coordination work.

**Core principle:** Fresh subagent per task + task review + broad final review = high quality, fast iteration

**Narration:** between tool calls, narrate at most one short line.

**Continuous execution:** Do not pause to check in with your human partner between tasks. Execute all tasks from the plan without stopping. The only reasons to stop are the four named below, or all tasks complete.

**Rulings, not stalls.** A running plan does not wait on a human. Conflicts,
ambiguities, plan defects — decide them. The spec is the binding authority, the
plan is its argument, and your judgment settles what neither answers. Record
every decision in the ledger as `Ruling: <what you decided> — <why> — <what it costs if wrong>`, and keep going.

Four things stop you, and only these: an irreversible or destructive
operation; a security-sensitive action; a side effect outside this worktree
that norms say you ask about first (a merge, a push, a publish); and a plan so
broken that every path forward is a guess.

## When to Use

**Use when:**
- You have an implementation plan with independent tasks
- Tasks can be dispatched to fresh subagents
- You want to stay in this session

**vs. executing-plans:** Use executing-plans when you lack subagent access or need a parallel session approach.

## Setup

Ensure the work happens in an isolated workspace: use
superpowers:using-git-worktrees to create one or verify the existing one.
Never start implementation on a main/master branch without your human
partner's explicit consent.

- Each plan owns a workspace: at skill start, run this skill's
  `scripts/sdd-workspace PLAN_FILE` — it prints the plan's git-ignored
  directory (`<repo-root>/.superpowers/sdd/<plan-basename>/`), home to
  every artifact for THIS plan: ledger, briefs, reports, review packages.
- Check for this plan's ledger at `<workspace>/progress.md`. If its first
  line names your plan file, tasks with a `Task <N>: complete` line are DONE
  — do not re-dispatch them; resume at the first task without one.
- Create the ledger with its identity as the first line:
  `# SDD ledger — plan: <plan file path>`.
- The ledger is your recovery map: after compaction, trust the ledger and
  `git log` over your own recollection.

Read the plan once, note its context and Global Constraints, and create a
todo per task. If the plan names a Spec, read that too.

Before dispatching Task 1, do a quick scan for obvious conflicts between
tasks (shared files, contradictory requirements). If you find conflicts, rule
on them and ledger the rulings. If the scan is clean, proceed.

## Model Selection

Use the least powerful model that can handle each role.

**Mechanical implementation tasks** (isolated functions, clear specs, 1-2 files): use a fast, cheap model.

**Integration and judgment tasks** (multi-file coordination, debugging): use a standard model.

**Architecture and design tasks**: use the most capable available model.
The final whole-branch review is one of these.

**Fix-loop escalation (round 3)**: use a model at least one tier above
the implementer that got stuck.

**Always specify the model explicitly when dispatching a subagent.** An
omitted model inherits your session's model — often the most expensive.

**Task complexity signals:**
- Touches 1-2 files with a complete spec → cheap model
- Touches multiple files with integration concerns → standard model
- Requires design judgment or broad codebase understanding → most capable model

## The Task Loop

**Batch small same-shape work.** When the plan lists several tasks that are
each a small, independent edit of the same kind, compose ONE dispatch brief
listing every file and its change, send the whole batch to a single subagent,
and review its diff as one unit.

Everything you paste into a dispatch prompt stays resident in your context.
Hand artifacts over as files.

### 1. Dispatch the implementer

Record BASE (`git rev-parse HEAD`) before dispatching.

- **Task brief:** run this skill's `scripts/task-brief PLAN_FILE N` — it
  extracts the task's full text to a file and prints the path. Your dispatch
  should contain: (1) one line on where this task fits; (2) the brief path;
  (3) interfaces from earlier tasks; (4) your resolution of any ambiguity;
  (5) the report-file path.
- **Report file:** name it after the brief (brief `…/task-N-brief.md` →
  report `…/task-N-report.md`) and put it in the dispatch prompt.
- A dispatch prompt describes one task, not the session's history. Do not
  paste accumulated prior-task summaries.
- The dispatch carries the no-subagents contract (in the implementer template).
- Never dispatch multiple implementation subagents in parallel (conflicts).

Template: [implementer-prompt.md](implementer-prompt.md)

### 2. Handle the report

**DONE:** Generate the review package (`scripts/review-package PLAN_FILE BASE HEAD`), then dispatch the task reviewer with the printed path.

**DONE_WITH_CONCERNS:** Read concerns. If about correctness/scope, address before review. If observations, note and proceed to review.

**NEEDS_CONTEXT:** Provide missing context and re-dispatch.

**BLOCKED:** Assess: context problem → provide more context; reasoning problem → re-dispatch with more capable model; task too large → break into pieces; plan wrong → rule, ledger, re-dispatch with ruling.

### 3. Review the task

For substantial tasks, behavior changes, integration-heavy work, public interfaces,
or risky logic: dispatch the task reviewer for both spec compliance and code quality.
For trivial mechanical tasks that are well-specified and strongly verified by tests,
the final whole-branch review provides the safety net — skip the per-task review.

- Hand the reviewer its diff as a file: run `scripts/review-package PLAN_FILE BASE HEAD`
  and pass the printed path.
- **Reviewer inputs:** the brief file, the report file, and the review package,
  plus the global constraints from the spec.
- Do not pre-judge findings for the reviewer.

Template: [task-reviewer-prompt.md](task-reviewer-prompt.md)

### 4. The fix loop

Triggers when the review reports spec failure, any Critical or Important finding,
or a verified gap.

- Record Minor findings in the ledger as deferred. They never enter the loop.
- A finding that conflicts with the plan text: rule on it, ledger the ruling.

Everything else enters the loop. Continue fix iterations when findings are
concrete and actionable and the previous iteration made meaningful progress.
Escalate or adjudicate when the same issue repeatedly survives, attempts are
not materially improving, or another retry is unlikely to help. **Three rounds
maximum as a safety cap.**

**Rounds 1-2 — resume the original implementer** with the open findings.

**Round 3 — dispatch a fresh implementer on a more capable model** with
the brief, report file, open findings, and context about prior attempts.

**Every round:** the implementer fixes, re-runs covering tests, appends
fix report. Dispatch a scoped re-review (`scripts/review-package PLAN_FILE FIX_BASE HEAD`, [re-review-prompt.md](re-review-prompt.md)).

**After each round,** append to the ledger:
`Task <N>: fix round <R>/3 (<X> addressed, <Y> open; commits <a7>..<b7>)`

The controller should delegate substantive implementation changes. It may make
trivial integration fixes, conflict resolutions, formatting corrections, or
obvious one-line fixes where dispatching another agent would cost materially
more than the edit.

**The breaker.** When the cap is reached or progress has stalled,
adjudicate each open finding yourself:

- **Reviewer is wrong or point is contestable:** park it with a ruling.
- **Real but nothing downstream builds on it:** park it, note it's real and deferred.
- **Real and load-bearing:** rule on the smallest change that unblocks
  dependent work, ledger it, and carry it into the next task's dispatch.

### 5. Complete the task

Append the completion line to the ledger:
`Task <N>: complete (commits <base7>..<head7>, review clean|<K> parked)`

Mark the todo complete and move on.

## Final Review

For substantial plan execution, run the final whole-branch review. Skip it
only if equivalent broad review evidence already exists on the same final tree.

Run `scripts/review-package PLAN_FILE MERGE_BASE HEAD` and dispatch the
final reviewer on the most capable available model, using
[code-reviewer.md](../requesting-code-review/code-reviewer.md). Point it
at the ledger's deferred-minor and parked lines.

If the final review returns findings, dispatch ONE fix subagent with all
findings, then run exactly one scoped re-review. Adjudicate residuals as
in the task loop's breaker. There is no second fix wave.

## Finish

Collect every ledger line containing `Ruling:` into your final message
under "Rulings I made", each with what it costs if wrong. This is the only
place your decisions reach your human partner.

When the final review is clean, delete this plan's workspace
(`rm -rf <workspace>`). Sibling directories belong to other plans.

Use superpowers:finishing-a-development-branch.

## Common Rationalizations

| Excuse | Reality |
|--------|---------|
| "Close enough on spec compliance" | Reviewer found spec gaps = not done. Fix or adjudicate at the cap. |
| "I'll fix it myself, dispatching is overhead" | OK for trivial one-liners. For substantive changes, delegate — controller fixes pollute context and skip review. |
| "The fix was small, skip the re-review" | Unreviewed fixes are how regressions land. |
| "Ledger bookkeeping is overhead" | The ledger is what survives compaction. Without one, controllers re-dispatch completed tasks. |
