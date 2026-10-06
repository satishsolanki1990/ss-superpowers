---
name: writing-readable-code
description: Use when writing or modifying code in any language — guides naming, comments, and structure for readability and maintainability
---

# Writing Readable Code

Code is read far more than it is written. Optimize for the reader.

**Related:** For file and folder organization, see superpowers:structuring-projects. For robustness and edge case coverage, see superpowers:test-driven-development — TDD supports implementation but should never be its sole driver.

## Naming

Name by what it **is** or **does**, not by type or implementation.

```
# Bad
d = 86400        # what is d?
xs = get(u, True) # what are xs, u?
strName = "Ada"  # type prefix adds nothing

# Good
seconds_per_day = 86400
active_users = get_users(include_archived=True)
name = "Ada"
```

- Use full words: `remaining_attempts`, not `rem_att`
- Booleans read as questions: `is_valid`, `has_children`, `can_retry`
- Functions read as actions: `send_invoice`, `parse_config`
- Avoid generic names: `data`, `info`, `temp`, `result` — say what data, what result
- Match the domain vocabulary the team already uses

## Comments

Comment the **why**, never the **what**. The code already says what.

```
# Bad — restates the code
i += 1  # increment i

# Good — explains a non-obvious reason
i += 1  # off-by-one: API pages are 1-indexed but our list is 0-indexed
```

When to comment:
- **Constraints**: "must run before X because..."
- **Workarounds**: "works around bug in library v2.3; remove after upgrade"
- **Business rules**: "90-day window per SOX compliance requirement"
- **Performance choices**: "O(n^2) is fine here — n is bounded at 50 by schema validation"

When NOT to comment:
- Restating what the code does
- Apologizing for bad code (fix it instead)
- Commented-out code (delete it; git remembers)

## Docstrings

Every public function and class gets a docstring. Both humans and AI agents
read these to understand intent without reading the body.

```
# Bad — no docstring, caller must read the implementation
def retry(fn, n, delay):
    ...

# Good — intent, params, and return value are clear
def retry(fn, max_attempts, delay_seconds):
    """Call fn up to max_attempts times, sleeping delay_seconds between tries.

    Returns the result of fn on success.
    Raises the last exception if all attempts fail.
    """
    ...
```

A good docstring answers:
- **What** the function/class does (one sentence)
- **Parameters** that aren't obvious from the name and type
- **Return value** — what it gives back and in what shape
- **Raises/errors** — what callers should expect to handle
- **Side effects** — if it writes to disk, sends a request, mutates state

Skip docstrings on trivial helpers where the name says everything (`is_empty`, `to_json`).

## Structure

**Small, focused units.** Each function does one thing and its name says what.

**Early returns over deep nesting:**

```
# Bad — hard to follow
def process(order):
    if order:
        if order.is_valid():
            if order.has_items():
                # actual logic buried 3 levels deep
                ...

# Good — guard clauses first
def process(order):
    if not order:
        return
    if not order.is_valid():
        raise InvalidOrderError(order.id)
    if not order.has_items():
        return

    # actual logic at top level
    ...
```

**Avoid boolean parameters that change behavior** — they hide two functions in one:

```
# Bad — caller must know what True means
send_email(user, True)

# Good — intent is obvious
send_email(user, include_attachment=True)
# or split: send_email(user) / send_email_with_attachment(user)
```

## Consistency

- Follow the conventions already in the codebase — even if you prefer different ones
- Don't mix styles in the same file (camelCase and snake_case, tabs and spaces)
- When no convention exists, pick one and apply it uniformly

## Dead Code

- Delete unused code; don't comment it out
- No `TODO` without a concrete trigger for when to act on it
- Remove feature flags and their dead branches once the feature ships
