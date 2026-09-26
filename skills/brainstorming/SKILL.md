---
name: brainstorming
description: "Use when building new features or components that involve meaningful design decisions, ambiguous requirements, or architectural changes — not needed for clear, well-scoped modifications to existing code"
---

# Brainstorming Ideas Into Designs

Turn ideas into approved designs through collaborative dialogue. Classify
the request, investigate read-only, surface the open decisions, and get your
human partner's explicit approval before any implementation.

## Investigate Before Designing

Confident premises about a codebase are often wrong. Before proposing a
design for a bounded or architectural change, do a read-only investigation:

- Read the code paths the change touches. Code is the source of truth;
  docs and comments drift, so confirm any doc claim against the code.
- Cite evidence as `path/to/file.py:123` for every claim the design rests on.
- Separate what you verified from what you assume.
- End with a list of **open questions and design decisions** your human
  partner needs to settle. Flag contradictions between the request, the
  code, and any recorded decisions (ADRs, decision logs) instead of
  resolving them silently.

No edits, scaffolding, dependency installs, or migrations during
investigation.

<HARD-GATE>
Do not take any implementation action (writing product code, scaffolding,
installing product dependencies, invoking an implementation skill) until
your human partner has explicitly approved the design for the selected path:

- Spike: a local, reversible, side-effect-free probe needs no approval;
  anything with cost, destructive actions, or external systems needs a nod.
- Bounded: approval of the short in-chat design and its open decisions.
- Architectural: approval of the design; if a written spec is warranted,
  approval of the written spec before writing-plans.

Present, then STOP and wait for an explicit yes. A reply approves only the
stage actually presented. Approval of a spike or an idea does not approve
the follow-up change.
</HARD-GATE>

## Three Paths

Before your first question, classify the request and say the classification out loud so your human partner can override it:

- **Spike** — a feasibility question ("can we...", "is it possible...",
  "quick and dirty is fine") whose output is an answer, not code you
  keep. If the investigation is local, reversible, and has no external
  side effects, investigate directly and report findings. If the probe
  involves meaningful cost, destructive actions, or external systems,
  present the plan briefly and get a nod first. No design doc, no spec
  file. Report findings as a recommendation; anything you built stays
  labeled throwaway.
- **Bounded** — a well-scoped change to code that already exists in
  this repo: a new flag, a small endpoint, a one-file fix.
  Understanding the kind of app is not enough — bounded means the flow
  you are changing is already here to read. If there is no existing
  flow to change, the task is not bounded. Ask the clarifying
  questions that matter, present a short design in chat (a few
  sentences to a few short paragraphs) with the open decisions, and
  STOP for approval. When the design is obvious from the codebase it
  can be one sentence stating what you'll do and where — the approval
  gate still applies. No spec file, no implementation plan document.
- **Architectural** — new projects, new subsystems, changes that
  restructure how components fit together or alter interfaces others
  depend on. Reason about the architecture, clarify important
  decisions, and present the design. A formal spec document and
  implementation plan are warranted when the result is substantial
  enough — not automatically for every architectural discussion.

When hidden complexity emerges mid-task, stop, say so, and upgrade the
path. When a task turns out simpler than expected, you may shrink the
design artifact, but not skip the approval gate.

## Red Flags

| Thought | Reality |
|---------|---------|
| "It's bounded and the design is obvious — I'll start while they read it" | The gate is the approval, not the design's length. Present, then stop until you hear yes. |
| "I know how this works, no need to read the code" | Investigate anyway. Confident premises regularly fail on contact with the code. |
| "The doc says X, so X" | Docs drift. Verify against the code and cite file:line. |
| "I'll pick the reasonable option and mention it later" | Open design decisions go to your human partner before implementation, not after. |
| "I understand this kind of app, so it's bounded" | Bounded measures the repo, not your familiarity. No existing flow means architectural. |
| "The spike works, so I'll keep the code" | A spike's output is an answer. Keeping the code is a new request — classify it. |

## Checklist

Classify first, announce the path, then work through the items for
your path.

**Spike:**
1. **Explore project context** — enough to frame the probe
2. **Investigate** — as cheaply as correctness allows. If the probe has external side effects or meaningful cost, present the plan briefly and get a nod first.
3. **Report findings** — a recommendation; label anything built as throwaway

**Bounded:**
1. **Investigate (read-only)** — the code paths touched, with file:line evidence
2. **Ask clarifying questions** — the ones that matter; ask related questions together
3. **Present short design in chat** — approach, files touched, testing, open decisions
4. **Get approval** — STOP and wait for an explicit yes
5. **Implement** — proceed with the normal development workflow; no plan document

**Architectural:**
1. **Investigate (read-only)** — files, docs, recent commits, recorded decisions; file:line evidence
2. **Offer the visual companion just-in-time** — NOT upfront. The first time a question would genuinely be clearer shown than described, offer it then (see Visual Companion section below).
3. **Ask clarifying questions** — understand purpose/constraints/success criteria; ask related questions together
4. **Propose approaches** — with trade-offs and your recommendation, only when multiple approaches are genuinely viable and their trade-offs materially matter; otherwise recommend the strongest straightforward solution
5. **Present design + open decisions** — scaled to complexity; STOP for explicit approval of the design and every open decision
6. **Write design doc** (when warranted) — save to `docs/superpowers/specs/YYYY-MM-DD-<topic>-design.md` and commit. A formal spec is warranted when the user requests one, the decision needs preservation for future developers, multiple people need to coordinate around it, or repository conventions require it.
7. **Spec self-review** (if spec written) — quick inline check for placeholders, contradictions, ambiguity, scope (see below)
8. **User reviews written spec** (if spec written) — STOP until approved
9. **Transition to implementation** — invoke writing-plans skill when the resulting implementation is substantial enough to benefit from formal task decomposition. Otherwise proceed directly.

**Terminal states are path-bound.** Architectural: after approval, if
formal planning is warranted, invoke writing-plans; otherwise implement
directly. Bounded: after approval, implement directly; no plan document.
Spike: the terminal state is a reported recommendation.

## The Process

The subsections below serve the bounded and architectural paths (a
spike stops at "present the probe, get a nod"). Sections from
**Exploring approaches** onward are architectural-path depth — for
bounded work, context plus a few questions plus a short in-chat design
is the whole process.

**Understanding the idea:**

- Investigate the current project state first (files, docs, recent commits) — read-only, with file:line evidence
- Before asking detailed questions, assess scope: if the request describes multiple independent subsystems, flag this immediately. Help decompose into sub-projects if needed.
- For appropriately-scoped projects, ask questions to refine the idea
- Prefer multiple choice questions when possible
- Focus on understanding: purpose, constraints, success criteria

**Exploring approaches:**

- Propose approaches with trade-offs only when genuinely needed
- Lead with your recommended option and explain why
- YAGNI ruthlessly - remove unnecessary features from every approach and design

**Presenting the design:**

- Scale each section to its complexity: a few sentences if straightforward, up to 200-300 words if nuanced
- Present the whole design, then ask for approval once; don't stop after every section
- Cover: architecture, components, data flow, error handling, testing
- End with the open decisions, each with your recommendation

**Design for isolation and clarity:**

- Break the system into smaller units that each have one clear purpose, communicate through well-defined interfaces, and can be understood and tested independently
- Smaller, well-bounded units are also easier to work with — you reason better about code you can hold in context at once, and your edits are more reliable when files are focused.

**Working in existing codebases:**

- Explore the current structure before proposing changes. Follow existing patterns.
- Where existing code has problems that affect the work, include targeted improvements as part of the design.
- Don't propose unrelated refactoring.

## After the Design (architectural path, when spec is written)

**Documentation:**

- Write the validated design (spec) to `docs/superpowers/specs/YYYY-MM-DD-<topic>-design.md`
  - (User preferences for spec location override this default)
- Use elements-of-style:writing-clearly-and-concisely skill if available
- Commit the design document to git

**Spec Self-Review:**
Scan for placeholders, internal contradictions, scope issues, and ambiguity. Fix inline.

**User Review:**
Ask the user to review the spec before proceeding. Only proceed once approved.

**Implementation:**
If the implementation is substantial enough to benefit from formal task decomposition, invoke the writing-plans skill. Otherwise proceed directly to implementation.

## Visual Companion

A browser-based companion for showing mockups, diagrams, and visual options during brainstorming. Available as a tool — not a mode.

**Offering the companion (just-in-time):** Do NOT offer it upfront. Wait until a question would genuinely be clearer shown than told — a real mockup / layout / diagram question, not merely a UI *topic*. The first time that happens, offer it then, as its own message:
> "This next part might be easier if I show you — I can put together mockups, diagrams, and comparisons in a browser tab as we go. It's still new and can be token-intensive. Want me to? I'll open it for you."

**This offer MUST be its own message.** If they accept, start the server with `--open`. If they decline, continue text-only and don't offer again unless they raise it.

**Per-question decision:** Even after the user accepts, decide FOR EACH QUESTION whether to use the browser or the terminal. Use the browser for content that IS visual (mockups, wireframes, layout comparisons, architecture diagrams). Use the terminal for content that is text (requirements questions, conceptual choices, tradeoff lists).

If they agree to the companion, read the detailed guide before proceeding:
`skills/brainstorming/visual-companion.md`
