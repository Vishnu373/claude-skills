---
name: implement-from-design
description: Turn a project's design.md architecture into a granular, phased implementation.md plan, then (only after the user approves the plan) build the project phase by phase, updating implementation.md as living progress documentation. Use when the user wants to implement/build a project from a design doc or turn an architecture into a plan and code.
---

# Implement From Design

Turn a project's architecture (`design.md`) into a granular, phased
`implementation.md` planning document, and then — **only once the user approves
that document** — build the project phase by phase, keeping `implementation.md`
updated as living progress documentation.

## Required reading order

You MUST read these in this exact order before writing anything. Do not skip
or reorder — each later source can override or constrain the earlier one.

1. **`design.md`** — the architecture / source of truth for *what* to build.
   Find it in the working directory (or ask the user for its path if absent).
2. **`CLAUDE.md`** — project conventions, constraints, tooling, and house
   rules for *how* to build. Read every `CLAUDE.md` in scope (repo root and any
   nested ones). If none exists, note that and proceed with sensible defaults.
3. **Only then** create / update **`implementation.md`**.

If `design.md` does not exist, stop and ask the user where the design lives —
do not invent an architecture.

## Step 1 — Read and understand

- Read `design.md` fully.
- Read `CLAUDE.md` fully.

## Step 2 — Ask clarifying questions

Before producing the plan, resolve ambiguity. Use the AskUserQuestion tool to
ask targeted, granular questions about anything the two documents leave unclear
or contradictory, for example:

- Scope of the first deliverable vs. later phases (MVP boundary).
- Unspecified tech choices (database, auth, hosting, libraries).
- Data model details, edge cases, and validation rules.
- Testing expectations (unit/integration/e2e, coverage bar).
- Environments, secrets, and deployment targets.
- Anything where `design.md` and `CLAUDE.md` disagree.

Ask in small batches, keep each question concrete, and break large unknowns
down to a granular level. Do not start writing `implementation.md` until the
blocking questions are answered.

## Step 3 — Write the implementation.md document

`implementation.md` is a **planning document**, not code. Break the project
into **multiple ordered phases**, organized into **two groups: Backend and
Frontend**. Every phase belongs to exactly one group. Each phase must be small,
independently verifiable, and build on the previous one. Use this structure:

```markdown
# Implementation Plan

> Source design: design.md
> Conventions: CLAUDE.md
> Generated: <date>

## Overview
<2–4 sentences: what we're building and the phasing strategy across the
Backend and Frontend groups.>

## Assumptions & Decisions
- <resolved clarifying questions and chosen defaults>

## Backend

### Phase B1 — <name>  `[ ] not started`
**Goal:** <one sentence>
**Depends on:** <none | Phase B/F-N>
**Tasks:**
- [ ] <granular task>
- [ ] <granular task>
**Deliverables:** <files/modules produced>
**Verification:** <how we know it works: tests, command, manual check>

### Phase B2 — <name>  `[ ] not started`
...

## Frontend

### Phase F1 — <name>  `[ ] not started`
**Goal:** <one sentence>
**Depends on:** <none | Phase B/F-N>
**Tasks:**
- [ ] <granular task>
- [ ] <granular task>
**Deliverables:** <files/modules produced>
**Verification:** <how we know it works: tests, command, manual check>

### Phase F2 — <name>  `[ ] not started`
...

## Progress Log
<append-only notes; updated after each phase>
```

Guidelines for grouping and phases:

- Split all phases into two top-level groups: **Backend** and **Frontend**.
  Number phases per group (`B1`, `B2`, … and `F1`, `F2`, …).
- Within each group, order phases by dependency: foundation/scaffolding first,
  then core domain, then features, then integration, then polish/deployment.
- Cross-group dependencies are allowed and must be stated explicitly in
  **Depends on:** (e.g. a frontend phase depending on a backend API phase).
  Generally sequence the backend phases that a frontend phase needs before it.
- If the project is purely backend or purely frontend, keep both headings but
  note the empty group as `_(none)_` rather than inventing work.
- Each phase should be completable and verifiable on its own.
- Tasks within a phase must be granular (a task ≈ a focused unit of work, not a
  whole subsystem).
- Every phase needs an explicit **Verification** step.

## Step 4 — Get approval (gate)

After writing `implementation.md`, present the phase breakdown to the user and
**ask for explicit approval**. This is a hard gate:

- **Do NOT write or modify any project/code files until the user approves the
  `implementation.md` document.**
- The only file produced before approval is `implementation.md` itself.
- Incorporate the user's edits and re-confirm until they approve.

## Step 5 — Implement phase by phase (after approval)

Only once the user has approved the document, work through the phases group by
group — honoring cross-group **Depends on:** links — for each phase in order:

1. Announce which group and phase you're starting (e.g. "Backend · Phase B1").
2. Implement its tasks, following `CLAUDE.md` conventions and `design.md`
   architecture.
3. Run the phase's verification (tests/build/lint or the stated manual check).
4. **Update `implementation.md`**:
   - Mark the phase status `[x] done` (or `[~] in progress` / `[!] blocked`).
   - Check off completed task checkboxes.
   - Append a dated entry to the **Progress Log** noting what was built, key
     decisions, deviations from the design, and any follow-ups.
5. Pause for the user to review before moving to the next phase, unless they've
   asked you to run straight through.

Never advance to the next phase until the current phase's verification passes
and `implementation.md` is updated to reflect it. A phase in one group may only
start once every phase it lists under **Depends on:** (in either group) is done.

## Rules

- Read order is non-negotiable: `design.md` → `CLAUDE.md` → write
  `implementation.md`.
- `implementation.md` is a document — first a plan, then a living progress log.
  No code is written until the user approves it.
- Phases are always organized into two groups, **Backend** and **Frontend**,
  with phases numbered per group (`B1`, `B2`, …, `F1`, `F2`, …) and cross-group
  dependencies stated explicitly.
- Keep `implementation.md` current after every phase; it is the single source
  of truth for progress.
- Prefer surgical, convention-matching changes over rewrites.
- If reality diverges from `design.md` mid-build, surface it to the user and
  record the deviation in the Progress Log rather than silently improvising.
