---
name: verification-before-completion
description: Use when about to claim work is complete or passing — requires running verification and confirming output before making success claims
---

# Verification Before Completion

## Overview

**Core principle:** Evidence before claims, always.

## The Gate

```
BEFORE claiming any status:

1. IDENTIFY: What command proves this claim?
2. RUN: Execute the command (or confirm you just ran it and read the output)
3. READ: Full output, check exit code, count failures
4. VERIFY: Does output confirm the claim?
   - If NO: State actual status with evidence
   - If YES: State claim WITH evidence
```

Existing verification evidence is valid when it was run against the same relevant working-tree state and no subsequent changes could invalidate it. If you modified code after the last test run, run tests again. If you only read files or wrote a response, the prior evidence still holds — cite it.

Passing tests prove test results, not necessarily that every requested requirement was implemented. Before claiming a task is complete, confirm both that relevant verification passes and that the requested acceptance criteria are satisfied.

## Common Failures

| Claim | Requires | Not Sufficient |
|-------|----------|----------------|
| Tests pass | Test command output: 0 failures | Previous run in a different context, "should pass" |
| Build succeeds | Build command: exit 0 | Linter passing, logs look good |
| Bug fixed | Reproduce original symptom: passes | Code changed, assumed fixed |

## Red Flags

- Using "should", "probably", "seems to" about verification status
- Expressing satisfaction before verification ("Great!", "Perfect!", "Done!")
- About to commit/push/PR without verification
- Trusting agent success reports without checking the diff

## Key Patterns

**Tests:**
```
GOOD: [Run test command] [See: 34/34 pass] "All tests pass"
BAD:  "Should pass now" / "Looks correct"
```

**Agent delegation:**
```
GOOD: Agent reports success -> Check VCS diff -> Verify changes -> Report actual state
BAD:  Trust agent report at face value
```
