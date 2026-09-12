---
name: requesting-code-review
description: Use when completing major features or before merging to verify work meets requirements
---

# Requesting Code Review

Dispatch a code reviewer subagent to catch issues before they cascade. The reviewer gets precisely crafted context for evaluation — never your session's history.

**Core principle:** Review early, review often.

## When to Request Review

**Recommended:**
- After completing a major feature
- Before merge to main
- When stuck (fresh perspective)
- After fixing a complex bug

**Not needed when:**
- Already reviewed through subagent-driven-development (which runs per-task reviews and a final whole-branch review)
- Change is trivial and well-tested

## How to Request

**1. Get git SHAs:**
```bash
BASE_SHA=$(git rev-parse HEAD~1)  # or origin/main
HEAD_SHA=$(git rev-parse HEAD)
```

**2. Dispatch code reviewer subagent:**

Dispatch a `general-purpose` subagent, filling the template at [code-reviewer.md](code-reviewer.md)

**Placeholders:**
- `{DESCRIPTION}` - Brief summary of what you built
- `{PLAN_OR_REQUIREMENTS}` - What it should do
- `{BASE_SHA}` - Starting commit
- `{HEAD_SHA}` - Ending commit

**3. Act on feedback:**
- Fix Critical issues immediately
- Fix Important issues before proceeding
- Note Minor issues for later
- Push back if reviewer is wrong (with reasoning)

## Common Rationalizations

| Excuse | Reality |
|--------|---------|
| "I'll just review the diff myself instead of dispatching a reviewer" | You're the coordinator — reviewing inline burns context. Dispatch a reviewer. |
| "The reviewer needs my whole session history" | Hand it precisely crafted context, never your session's history. |

See template at: [code-reviewer.md](code-reviewer.md)
