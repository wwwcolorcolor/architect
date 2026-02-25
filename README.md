# /architect

A Claude Code skill for building software projects through a staged methodology that prevents the #1 AI coding failure: losing context on a big plan and missing pieces.

## Prerequisites

None. This skill is pure methodology — no external tools or API keys required.

## Install

**Claude Code:**

```bash
claude skill add --url https://github.com/wwwcolorcolor/architect-skill
```

**Other tools** (Cursor, Windsurf, Copilot, Gemini, Codex, Aider, Cline, etc.):

Copy the contents of [`SKILL.md`](./SKILL.md) into your tool's rules file:

| Tool | File |
|------|------|
| Cursor | `.cursorrules` or `.cursor/rules` |
| Windsurf | `.windsurfrules` |
| Copilot | `.github/copilot-instructions.md` |
| Gemini | `.gemini/style-guide.md` |
| Codex | `AGENTS.md` |
| Aider | `.aider/conventions.md` |
| Cline / Roo Code | `.clinerules` |

## Usage

```
/architect               — start a new project or resume an existing one
/architect <description> — assess scope and begin planning
/architect resume        — pick up where you left off
```

### Examples

```
/architect a CLI tool that syncs local files to S3
/architect resume
/architect I want to build a multiplayer drawing app with websockets
```

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

## What gets created

```
project/
├── PROJECT_STATE.md        # Current snapshot (machine-readable)
├── notebook/               # Design notes, lessons (compatible with /notebook skill)
│   ├── _index.md           # Rich index of all notes
│   ├── lessons.md          # Anti-loop cheat sheet — failures and gotchas
│   └── ...
├── build_plan/             # Stage documents
│   ├── _build-order.md
│   └── stage-NN-*.md
├── src/                    # Code (built stage by stage)
└── tests/                  # Tests (grow with each stage)
```

## How it prevents context loss

The core rule: never work from one giant plan. Atomize into stages small enough to hold entirely in context. Stage documents are written 2-3 at a time (not all upfront) because later stages written before earlier stages are built are based on assumptions that will be wrong.

If something breaks mid-build, the methodology has explicit protocols for going back, fixing, and propagating changes through dependent stages.

## Works with /notebook

Architect uses the same `notebook/` folder as the [notebook skill](https://github.com/wwwcolorcolor/notebook-skill). If you have both installed, `/save` works seamlessly inside architect projects to capture failures and lessons during the build phase. If you only have architect, the notes infrastructure is built in — notebook is not required.
