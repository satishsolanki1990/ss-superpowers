---
name: structuring-projects
description: Use when creating new projects, adding modules, or reorganizing file and folder layout — guides grouping, colocation, naming, and file granularity
---

# Structuring Projects

A clear file structure lets you find code by intuition, not by searching.
Organize by what the code *does*, not what it *is*.

## Grouping

**Group by feature or domain, not by file type.**

```
# Bad — finding "orders" means checking 5 directories
controllers/
  orders.py
models/
  orders.py
services/
  orders.py
tests/
  test_orders.py
utils/
  order_helpers.py

# Good — everything about orders lives together
orders/
  routes.py
  model.py
  service.py
  helpers.py
  tests/
    test_service.py
    test_routes.py
```

Layer-based grouping (`controllers/`, `models/`) is acceptable only in
small projects where the total file count stays navigable. Once you need
to scroll to find a file, regroup by feature.

## Folder Naming

- Lowercase, hyphen-separated: `user-auth/`, `payment-processing/`
- Name by what it contains: `notifications/`, not `handlers/`
- Avoid generic names: `utils/`, `helpers/`, `misc/` — redistribute into the features that use them
- Match the domain vocabulary the team already uses

## Colocation

Keep related files next to their source:

- Tests beside the code they test, not in a distant `tests/` tree
- Types/interfaces beside the module that defines the concept
- Styles/templates beside the component that uses them
- Fixtures and test data beside the tests that need them

Colocation means deleting a feature deletes its tests, types, and
fixtures in one pass — nothing orphaned.

## File Granularity

**One concept per file** as the default.

Split when:
- A file has multiple classes/components that change for independent reasons
- You need to import one thing but the file pulls in unrelated dependencies
- The file exceeds what you can hold in context (~300-500 lines is a signal, not a rule)

Merge when:
- Two files always change together and are never used independently
- A "helper" file has one function used by one caller

## Entry Points

- Every project has a clear top-level entry (`main.py`, `index.ts`, `App.tsx`)
- Use index/barrel files to define a module's **public API** — only export what consumers need
- Don't re-export everything: a barrel that exports all internals defeats its purpose

```
# Good — barrel defines the public surface
orders/
  __init__.py      # exports: create_order, OrderStatus
  service.py       # internal
  model.py         # internal
  validation.py    # internal
```

## Existing Codebases

- Follow the established structure, even if you'd organize differently from scratch
- Propose structural changes only when the current layout actively harms the work (circular dependencies, impossible-to-test modules, files that force unrelated changes together)
- When restructuring, move files in a dedicated commit before changing behavior — never both at once
