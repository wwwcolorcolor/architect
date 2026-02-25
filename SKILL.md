---
name: architect
description: >
  Staged build methodology for architecting and building software projects. Drives the full
  lifecycle: technical spike, design notes, build planning, and stage-by-stage execution with
  professional-grade testing. Use when: (1) user says "/architect", "architect this",
  "new project", "start a project", "build this from scratch", (2) user wants to plan and
  build a software project from scratch using a structured methodology, (3) user wants to
  resume an in-progress project that uses the staged build methodology, (4) user references
  the build methodology or staged build process. NOT for: quick scripts, one-off fixes, or
  tasks that don't warrant a full project structure. If the task is small enough to build
  in one shot without planning, just build it directly — don't force the methodology.
---

# Staged Build Methodology

Drive software projects through a structured lifecycle that prevents the #1 AI coding failure: losing context on a big plan and missing pieces.

**Core rule:** Never work from one giant plan. Atomize into stages small enough to hold entirely in context, execute correctly, and validate before moving on.

For full methodology details: read `references/methodology.md`.
For testing obligations: read `references/testing.md`.
For stage document format: read `references/stage-template.md`.

## Invocation

Parse what follows the trigger:

| Input | Action |
|-------|--------|
| Bare invocation (`/architect`) | Ask: "New project or resuming an existing one?" |
| With project description | Assess scope (see Scaling Tiers), then start Phase 0 |
| `resume` or project name | Run Resume Protocol below |

## Scaling Tiers

Before starting, assess project scope to determine which tier applies:

| Tier | Signal | Phases Used | Notes |
|------|--------|-------------|-------|
| **Light** | Single feature, 1-2 files, < 500 lines | Phase 0 (quick) → Phase 2 (1-2 stages) → Execute | Skip formal design notes. Spike + stage docs is enough. |
| **Standard** | Multi-component system, 3-8 stages | All phases | Full methodology. |
| **Heavy** | 8+ stages, multiple subsystems, external integrations | All phases + milestone grouping | Group stages into milestones (3-4 stages each). Review plan at milestone boundaries. |

**How to assess:** Count the major components. If you can list them on one hand, it's Light. If you need two hands, Standard. If you start losing track, Heavy.

If unsure, ask the user: "This feels like a [tier] project — [X] major components. Does that match your sense of the scope?"

## Phase Sequence

```
Phase 0  → Technical Spike     → Validate hardest constraint
Phase 1  → Design Notes        → One note per domain, no code
Phase 1.5 → Build Order        → Dependency graph, stage sequence
Phase 2  → Stage Documents     → Write 2-3 stages ahead, not all upfront
Execute  → Build Stage by Stage → Verify, commit, next
```

Never skip phases. Never move forward until the current phase's gate check passes.

---

## Phase 0: Technical Spike

**Trigger:** Start of any new project.

1. Ask: "What's the riskiest technical assumption in this project?"
2. If user isn't sure, analyze the project and identify it yourself — state what you think the risk is and why
3. Design the smallest possible test (< 50 lines) that gives a YES/NO answer
4. Build and run the test
5. Result:
   - YES → Record the validated constraint, proceed to Phase 1
   - NO → Surface the problem, discuss alternatives, re-spike if needed

**Gate check:** `PROJECT_STATE.md` exists and contains a validated constraint under `## Core Constraints` with status `validated`. If this file doesn't exist or the constraint isn't validated, do not proceed.

---

## Phase 1: Design Notes

**Trigger:** Phase 0 gate check passes.

**Mode:** Planning only. Do not write any production code.

### Conversation Protocol

This phase is a structured conversation, not a monologue. Follow this loop for each domain:

1. **Draft:** Write a first-pass note for the domain based on what you know and can infer
2. **Present:** Show the user the key decisions and architecture, highlight what you're uncertain about
3. **Ask:** "What am I missing? What's wrong? What would you change?"
4. **Revise:** Update the note based on feedback
5. **Confirm:** "This note covers [domain]. Ready to move to [next domain]?"

Do NOT dump 10 questions at the user. Do NOT write notes in isolation without feedback. Each note is a collaborative artifact.

### Domain Discovery

Start with `00-project-overview.md` (what are we building and why), then identify domains using this checklist:

**Every project has these (always write notes for):**
- Data model / storage (what data exists, where it lives)
- Core logic (the thing the app actually does)
- Interface (API, CLI, UI — how users interact)
- Configuration (settings, env vars, secrets)

**Most projects also have (write notes if applicable):**
- Authentication / authorization
- External service integrations
- Error handling strategy
- Deployment / infrastructure

**Some projects need (write notes if relevant):**
- Background jobs / queues
- Caching strategy
- Real-time / WebSocket layer
- Search
- File storage / media handling

For each applicable domain, produce a note in `notes/`. Number sequentially. Maintain `notes/_index.md` as notes are created.

Finish with `NN-tech-stack.md` (justify every choice) and `NN-project-status.md` (what's specced, what's remaining).

### Gate Check

Before proceeding, verify ALL of these:

1. `notes/_index.md` exists and lists all notes
2. At least `00-project-overview.md` + one note per identified domain exists
3. `NN-tech-stack.md` exists with justified choices
4. No notes have unresolved `## Open Questions` that would block build planning
5. User confirms: "Design notes look complete, ready to plan the build?"

If any check fails, keep working on notes.

---

## Phase 1.5: Spec-to-Plan Translation

**Trigger:** Phase 1 gate check passes.

1. List every major component from the notes
2. Map dependencies (what needs what to exist first)
3. Sequence into stages:
   - Foundation first (project structure, config, health checks)
   - Data layer before logic
   - Core abstractions before implementations
   - Each stage independently testable
   - Stubs establish contracts for later stages
4. Write `build_plan/_build-order.md` with the stage list and dependency graph
5. Identify any stages that could run in parallel
6. Present the build order to the user for review before proceeding

**Gate check:** `build_plan/_build-order.md` exists with numbered stages, dependencies, and key outcomes. User has reviewed and approved the order.

---

## Phase 2 + Execute: Write and Build Incrementally

**Critical change from naive approach:** Do NOT write all stage documents upfront. Write 2-3 stage documents ahead, build those stages, then write the next batch. Why: later stage docs written before earlier stages are built are based on assumptions. Those assumptions will be wrong. Build validates reality.

### The Loop

```
1. Write stage docs for the next 2-3 stages
2. Build those stages (Execute protocol below)
3. After building, assess: does the build order still make sense?
   - YES → write next 2-3 stage docs, continue
   - NO  → update _build-order.md, adjust remaining stages, continue
4. Repeat until done
```

### Writing Stage Documents

For each stage, produce a document in `build_plan/` following the format in `references/stage-template.md`.

Every stage document must include:
1. Overview with explicit dependencies and key outcomes
2. Architecture diagram
3. Directory structure (every file created or modified)
4. Full implementation code (not pseudocode)
5. Configuration changes
6. **Test strategy and full test code** — read `references/testing.md` for obligations
7. Verification checklist (specific commands and expected outputs)
8. Debugging playbook (ranked failure modes with diagnostics)
9. Acceptance criteria
10. Next stage preview

### Executing a Stage

For each stage:

1. Read the stage document fully before writing any code
2. Build exactly what it specifies — no improvisation
3. Run the verification checklist
4. Fix anything that fails
5. All tests green, no skipped tests
6. Update `PROJECT_STATE.md` (see State Tracking below)
7. Commit
8. Move to next stage

### When Things Break

**Test failure in current stage:** Fix before moving on. Never carry broken tests.

**Plan is wrong:** Stop building. Update the stage document to match reality. Re-execute from the updated plan. Do not improvise a fix and leave the stage doc outdated.

**Prior stage bug discovered:**
1. Stop current stage work
2. Go back to the broken stage
3. Fix the bug
4. Re-run that stage's verification checklist — all green
5. Check if the fix changes interfaces that later stages depend on
6. If yes: update affected stage docs before continuing
7. Resume current stage

**Scope creep:** If you're adding things not in the stage doc, stop. Either add it to a future stage in `_build-order.md` or discard it.

---

## State Tracking

`PROJECT_STATE.md` is the single source of truth for where the project is. Update it after every phase transition and every completed stage.

### Required Format

```markdown
# [Project Name]

## Current Phase
phase: [0 | 1 | 1.5 | 2 | execute]
current_stage: [N or null]
last_completed_stage: [N or null]
tier: [light | standard | heavy]

## Core Constraints
| Constraint | Validated? | Result |
|------------|-----------|--------|
| [description] | yes/no | [what we found] |

## Completed
- [x] Phase 0: [constraint validated]
- [x] Phase 1: [N notes written]
- [x] Stage 1: [name] — [date]
- [ ] Stage 2: [name] — in progress

## Current State
**What exists:** [one line]
**What works:** [bullet list]
**What's broken:** [bullet list or "nothing"]
**Blocked on:** [description or "nothing"]

## Next Action
[Explicit single next step — not a list of possibilities, THE thing to do next]

## Build Order Reference
[Copy of stage list from _build-order.md with completion status]
```

### Update Rules
- Update `Current Phase` and `current_stage` on every phase/stage transition
- Move completed items to `## Completed` with dates
- `## Next Action` always contains exactly one actionable step
- If blocked, `## Blocked on` must explain why and what would unblock it

---

## Resume Protocol

When resuming (user says "resume", provides a project path, or PROJECT_STATE.md exists):

1. Read `PROJECT_STATE.md` — parse `Current Phase`, `current_stage`, `last_completed_stage`
2. Read `notes/_index.md` (if exists)
3. Read `build_plan/_build-order.md` (if exists)
4. If in execute phase, read the current stage document
5. Summarize back to user:
   - "You're in Phase [X], working on [description]"
   - "Last completed: [what]"
   - "Next action: [what]"
6. Ask: "Has anything changed since last session? Any new constraints or direction changes?"
7. Wait for confirmation, then continue from `## Next Action`

If `PROJECT_STATE.md` is missing or corrupted, scan the project directory for artifacts (notes/, build_plan/, src/) and reconstruct state from what exists. Ask the user to confirm before proceeding.

---

## Project Directory Structure

```
project/
├── PROJECT_STATE.md           # Current snapshot (machine-readable)
├── notes/                     # Phase 1 output
│   ├── _index.md
│   ├── 00-project-overview.md
│   └── ...
├── build_plan/                # Phase 2 output
│   ├── _build-order.md
│   ├── stage-01-foundation.md
│   └── ...
├── src/                       # Code (built stage by stage)
└── tests/                     # Tests (grow with each stage)
```

## Identity Rules

Before creating any project directory or git repo, ask the user which identity context this project belongs to. Respect all identity separation rules from the global config.
