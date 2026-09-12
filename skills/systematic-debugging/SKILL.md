---
name: systematic-debugging
description: Use when debugging complex, persistent, or unclear failures that resist quick diagnosis — not needed when the cause is already obvious from the error message
---

# Systematic Debugging

## Overview

**Core principle:** Find root cause before attempting fixes. Symptom fixes are failure.

## When to Use

**Use the full process for:**
- Failures with unclear root cause
- Bugs that have resisted an initial fix attempt
- Issues in multi-component systems where the failing layer is uncertain
- Performance problems or intermittent failures
- Situations where you don't fully understand why something is broken

**Quick investigation is fine for:**
- Obvious errors with clear messages (e.g., typo in variable name, missing import)
- Build failures with specific error pointing to the exact fix
- Issues where the root cause is immediately visible from the error

Even for quick fixes, read the error message carefully and verify the fix works. If a "quick fix" doesn't work on the first try, switch to the full process.

## The Four Phases

### Phase 1: Root Cause Investigation

**BEFORE attempting ANY fix:**

1. **Read Error Messages Carefully**
   - Don't skip past errors or warnings
   - Read stack traces completely
   - Note line numbers, file paths, error codes

2. **Reproduce Consistently**
   - Can you trigger it reliably?
   - If not reproducible, gather more data — don't guess

3. **Check Recent Changes**
   - What changed that could cause this?
   - Git diff, recent commits, new dependencies, config changes

4. **Gather Evidence in Multi-Component Systems**

   When system has multiple components, add diagnostic instrumentation at component boundaries before proposing fixes. Run once to see WHERE it breaks, then investigate that specific component.

5. **Trace Data Flow**

   See `root-cause-tracing.md` in this directory for the complete backward tracing technique. Quick version: where does the bad value originate? Keep tracing up until you find the source. Fix at source, not at symptom.

### Phase 2: Pattern Analysis

1. **Find Working Examples** — locate similar working code in same codebase
2. **Compare Against References** — if implementing a pattern, read reference implementation completely
3. **Identify Differences** — what's different between working and broken?
4. **Understand Dependencies** — what other components, settings, environment does this need?

### Phase 3: Hypothesis and Testing

1. **Form Single Hypothesis** — "I think X is the root cause because Y"
2. **Test Minimally** — smallest possible change to test hypothesis, one variable at a time
3. **Verify Before Continuing** — if it worked, move to Phase 4. If not, form NEW hypothesis — don't add more fixes on top.

### Phase 4: Implementation

1. **Create Failing Test Case** — simplest possible reproduction. Use the `superpowers:test-driven-development` skill for writing proper failing tests.
2. **Implement Single Fix** — address the root cause, ONE change at a time, no "while I'm here" improvements
3. **Verify Fix** — test passes? No other tests broken? Issue resolved?
4. **If 3+ Fixes Failed: Reassess Fundamentally** — stop patching and re-examine. Check for incorrect reproduction, environment differences, hidden state, concurrency issues, external dependency behavior, incorrect assumptions, test/tooling problems, or architectural mismatch. Discuss with your human partner before attempting more fixes.

## Common Rationalizations

| Excuse | Reality |
|--------|---------|
| "Just try this first, then investigate" | First fix sets the pattern. Do it right from the start. |
| "I see the problem, let me fix it" | Seeing symptoms =/= understanding root cause. |
| "Multiple fixes at once saves time" | Can't isolate what worked. Causes new bugs. |
| "One more fix attempt" (after 2+ failures) | 3+ failures = something fundamental is wrong. Stop patching and reassess. |

## Quick Reference

| Phase | Key Activities | Success Criteria |
|-------|---------------|------------------|
| **1. Root Cause** | Read errors, reproduce, check changes, gather evidence | Understand WHAT and WHY |
| **2. Pattern** | Find working examples, compare | Identify differences |
| **3. Hypothesis** | Form theory, test minimally | Confirmed or new hypothesis |
| **4. Implementation** | Create test, fix, verify | Bug resolved, tests pass |

## Supporting Techniques

These techniques are part of systematic debugging and available in this directory:

- **`root-cause-tracing.md`** - Trace bugs backward through call stack to find original trigger
- **`defense-in-depth.md`** - Add validation at multiple layers after finding root cause
- **`condition-based-waiting.md`** - Replace arbitrary timeouts with condition polling
