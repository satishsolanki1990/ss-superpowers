---
name: using-superpowers
description: Use when starting any conversation - establishes how to find and use skills
---

<SUBAGENT-STOP>
If you were dispatched as a subagent to execute a specific task, ignore this skill.
</SUBAGENT-STOP>

## The Rule

Check for relevant skills before starting work. If a skill clearly applies, invoke it — announce "Using [skill] to [purpose]" and follow the skill. For substantial multi-step workflows, create todos to track progress. Do not mechanically convert reference checklists, quality criteria, or short verification lists into todos.

Match the skill to the work:
- **Process skills** (brainstorming, systematic-debugging) set the approach when the task warrants it.
- **Quality skills** (writing-readable-code) apply during implementation to guide code readability and maintainability.
- **Implementation skills** carry out the work.

For clear, localized tasks where the right action is obvious, proceed directly — not every task needs a skill. Skills add value when there is genuine ambiguity, complexity, risk, or architectural impact.

## Skill Priority

When multiple skills apply, process skills come first.

- "Let's build X" → consider superpowers:brainstorming if design decisions are needed, then implementation skills.
- "Fix this bug" → consider superpowers:systematic-debugging if the cause isn't obvious, then domain skills.
- Writing or modifying code → superpowers:writing-readable-code applies throughout.

## Red Flags

| Thought | Reality |
|---------|---------|
| "I remember this skill" | Skills evolve. Read the current version. |
| "Let me do a few things first, then check skills" | Check for skills before acting. |
| "This definitely doesn't need any skill" | At least consider whether one applies. |

## Deferrals

Whenever you defer something — scope cut from a design, a Minor review
finding, optional cleanup, performance work without a signal — record it
where the work is tracked (spec, plan, ledger, or final message) with:

- What is deferred
- Why it is not being built now
- The concrete trigger to reconsider: a measurable threshold, a
  user-facing signal, a dependency landing, a product decision, or a usage
  or scale milestone
- Any measurement needed to evaluate that trigger

"Later," "future improvement," and other time-only statements are not
triggers.

## Platform Adaptation

If your harness appears here, read its reference file for special instructions:

- Claude Code: `references/claude-code-tools.md`
- Codex: `references/codex-tools.md`
- Pi: `references/pi-tools.md`
- Antigravity: `references/antigravity-tools.md`
- Hermes Agent: `references/hermes-tools.md`
- Muse: `references/muse-tools.md`

## User Instructions

User instructions (CLAUDE.md, AGENTS.md, GEMINI.md, etc, direct requests) take precedence over skills, which in turn override default behavior. Only skip skill workflows or instructions when your human partner has explicitly told you to.
