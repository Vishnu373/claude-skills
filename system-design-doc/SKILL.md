---
name: system-design-doc
description: Author a system design document interactively, one section at a time, through back-and-forth conversation. Breaks each section into granular questions before writing, then saves the result to design.md in the project root. Use when the user wants to write, draft, or flesh out a system/architecture design doc.
---

# System Design Doc

Co-author a system design document with the user. This is **not** a one-shot generation task. You interview the user section by section, drill each section down to a granular level through dialogue, agree on the content, then write it down. The final artifact is `design.md` in the project root.

## Core operating rules

1. **One section at a time.** Never draft the whole document in a single pass. Finish a section (ask → discuss → confirm → write) before moving to the next.
2. **Granular before writing.** Break each section into its sub-questions and ask them. Do not write prose until you have the specifics from the user. Don't invent requirements, scale numbers, or constraints — extract them.
3. **Back-and-forth, then write down.** After each section is agreed, append it to `design.md` immediately so the doc grows incrementally and the user sees progress. Re-read/append rather than rewriting the whole file each time.
4. **Propose, don't dictate.** Offer concrete options with trade-offs and a recommendation, but let the user decide. Use their domain knowledge.
5. **Challenge gaps.** If an answer is vague ("it should be fast"), push for numbers ("p99 < 200ms? how many RPS?").
6. **Adapt the outline.** The sections below are the baseline. If the project needs extra sections (e.g. Security & Auth, Observability, Migration Plan, Compliance, Rollout), add them where they fit and tell the user you're adding them.

## Workflow

### Step 0 — Kickoff
Before touching any section, get the lay of the land. Ask the user (batch these):
- What are we designing? One-line description.
- Who are the users / consumers?
- What's the scale ballpark (users, RPS, data volume, growth)?
- Any hard constraints up front (existing stack, cloud provider, budget, deadline, team size)?
- Greenfield or evolving an existing system?

Then confirm the section list you'll walk through (the baseline below plus any project-specific ones you've spotted). Create `design.md` with a title and a table of contents, then begin.

### Step 1..N — Walk each section

For **every** section: (a) state what the section captures, (b) ask the granular sub-questions, (c) discuss options/trade-offs, (d) on agreement, write that section into `design.md`, (e) briefly recap and move on. Confirm before advancing.

---

## Section playbook

Use these prompts to drive each section granularly.

### Overview (concise)
Keep it tight — a few sentences plus optional context. Drill on:
- Problem statement: what pain does this solve?
- Goals (what's in scope) and **non-goals** (explicitly out of scope).
- Key assumptions and constraints.
Write a short paragraph + a "Goals / Non-goals" list. Resist bloat here.

### Functional Requirements
What the system **does**. Drill on:
- Core user flows / use cases, listed discretely.
- For each: actor, trigger, expected behavior, success outcome.
- Must-have vs nice-to-have (MoSCoW or P0/P1/P2).
- Edge cases and explicit out-of-scope behaviors.
Write as a numbered/prioritized list of capabilities.

### Non-Functional Requirements
The qualities/constraints. Drill on concrete numbers for each relevant one:
- **Scale**: users, RPS (avg/peak), data size, growth rate.
- **Latency**: target p50/p99 per critical path.
- **Availability**: SLA target (99.9%? 99.99%?), RTO/RPO.
- **Consistency**: strong vs eventual, where it matters.
- **Durability**, **Security/compliance**, **Privacy**.
- **Maintainability / extensibility**, **Cost ceiling**.
Write as a table: requirement → target → rationale. Push back on any "fast/scalable/reliable" without a number.

### Diagram (application flow)
**Do not cram everything into one giant diagram.** Split into multiple focused diagrams. Decide together which views are needed:
- High-level context / system boundary (C4-style context).
- Per-module or per-service component diagrams.
- Key request/data flow sequences (one per critical path).
- Deployment topology if relevant.
Render with **Mermaid** code blocks inside `design.md` (```mermaid```), one per concern with a heading and a sentence explaining it. Ask which flows matter most and diagram those first.
- If the user wants polished visual diagrams, offer the `excalidraw-diagram` skill as a complement.

### API Design
Drill per endpoint / interface:
- Protocol/style: REST, gRPC, GraphQL, events/queue — and why.
- For each endpoint: method, path, request schema, response schema, status/error codes, auth, idempotency, pagination, rate limits.
- Versioning strategy.
Write as a per-endpoint spec (table or code blocks with example payloads).

### Data Model
Drill on:
- Core entities and their relationships (draw an ER Mermaid diagram).
- Storage engine choice per entity (SQL/NoSQL/cache/blob/search) and why.
- Schema per entity: fields, types, keys, indexes.
- Access patterns → confirm the schema/indexes serve them.
- Partitioning/sharding, retention, and consistency notes.
Write schemas (DDL-ish or tables) + an ER diagram.

### Trade-offs & Alternatives
For each major decision (datastore, sync vs async, monolith vs services, build vs buy, etc.):
- The options considered.
- Pros/cons of each.
- The choice made and **why**.
- What you'd revisit if assumptions change.
Write as decision records: Decision → Options → Chosen → Rationale.

### Pricing
Estimate the cost of running this. Drill on:
- Cloud provider / managed services in play.
- Per-component cost drivers (compute, storage, egress, requests, third-party APIs, licensing).
- Estimate at expected scale, and a rough scaled-up figure.
- Cost optimization levers and the main cost risk.
Write a cost-breakdown table with monthly estimates and assumptions. State that figures are estimates.

### Future Considerations (if any)
- Known limitations of the current design.
- Planned/likely next phases.
- Scaling milestones (what breaks at 10x and the plan).
- Tech debt knowingly accepted.
Only include if there's real substance — don't pad.

### Project-specific sections (add as needed)
Watch for the need to add, e.g.: Security & AuthN/Z, Observability & Monitoring, Failure Modes & Resilience, Caching Strategy, Migration/Rollout Plan, Compliance & Data Governance, Internationalization, Disaster Recovery. Propose them when the conversation surfaces the need.

---

## Output

- File: `design.md` in the **project root**.
- Structure: title, table of contents, then the sections in the order above (plus any added).
- Keep diagrams as Mermaid; keep tables for NFRs, API, data model, pricing.
- Update the file incrementally after each section — the user should be able to watch it fill in.

## Done criteria
Every agreed section is present in `design.md`, diagrams are split sensibly (not one mega-diagram), NFRs and pricing carry real numbers, and trade-offs record the *why*. Do a final read-through with the user and offer to adjust.
