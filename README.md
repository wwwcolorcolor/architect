# /architect

A Claude Code skill for building software projects through a staged methodology that prevents the #1 AI coding failure: losing context on a big plan and missing pieces.

## What it does

Drives projects through a structured lifecycle:

1. **Technical Spike** — validate the riskiest assumption before writing any code
2. **Design Notes** — collaborative domain-by-domain planning (no code yet)
3. **Build Planning** — dependency graph, stage sequencing
4. **Stage-by-Stage Execution** — write 2-3 stage docs ahead, build, verify, repeat

Each stage is small enough to hold entirely in context, execute correctly, and validate before moving on.

## Scaling

Automatically adapts to project scope:

- **Light** (1-2 files) — quick spike + minimal stages
- **Standard** (3-8 stages) — full methodology
- **Heavy** (8+ stages) — milestone grouping with review boundaries

## Install

```bash
claude skill add --url https://github.com/wwwcolorcolor/architect
```

## Usage

```
/architect              — start a new project or resume an existing one
/architect <description> — assess scope and begin planning
/architect resume        — pick up where you left off
```

## What gets created

```
project/
├── PROJECT_STATE.md        # Current snapshot (machine-readable)
├── notes/                  # Design notes per domain
│   ├── _index.md
│   └── ...
├── build_plan/             # Stage documents
│   ├── _build-order.md
│   └── stage-NN-*.md
├── src/                    # Code (built stage by stage)
└── tests/                  # Tests (grow with each stage)
```
