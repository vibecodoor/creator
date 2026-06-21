# Project Creator

> A Claude Code / agent skill that turns a raw product idea into a ready-to-build foundation — through deep parallel web research and a lean, self-maintaining project memory.

When you start a new project, you usually either dive in blind or drown in a wall of planning docs you'll never re-read. This skill does neither: it clarifies the idea, fans out **3–7 parallel research agents** (competitors, stack, pitfalls, and any separable subsystem), then lays down a **[Dev Memory Kit](https://github.com/vibecodoor/project-memory-kit)** — a thin always-loaded control plane plus on-demand surfaces — so your agent can resume cold without re-explaining the project every session.

## Who this is for

- **Solo builders / indie hackers** kicking off a new app who want decisions, not a menu.
- **Anyone using an agentic coding tool** (Claude Code, Codex, Cursor, Cline, Gemini CLI, …) who wants project state that survives `/compact` and new sessions.
- **Teams** who want a consistent, low-overhead "how this project is made" layout in every repo.

## What you get

After a run, your project gets a **`devkit/`** folder (so the memory files don't mix with your code/docs) containing:

- **`devkit/PROJECT.md`** — the stable control plane: vision, scope, architecture, invariants, build plan, and an embedded **Dev Memory Protocol** that tells any agent how to maintain the kit.
- **`devkit/RESEARCH.md`** — the evidence base: competitor analysis, recommended stack (with confidence tags), architecture patterns, "don't hand-roll" list, risks, and key resources.
- **`devkit/STATE.md`** — the live cursor: current task, next steps, blockers.

Plus bundled `templates/` for `DECISIONS.md`, `JOURNAL.md`, and per-feature `specs/` — born **on demand**, never pre-created as empty stubs.

> These files follow the **[Project Memory Kit](https://github.com/vibecodoor/project-memory-kit)** layout. `creator` is the research-driven *bootstrapper* for that kit — it fills the templates from real research instead of leaving you a blank `PROJECT.md`. If you just want the kit itself (templates + protocol, no research step), use that repo directly.

## Quick start / Install

This is a standard agent skill (root `SKILL.md`), so it installs into your agent's skills directory.

**1. With [`skills`](https://github.com/vercel-labs/skills) (recommended)** — auto-places the skill for 70+ agents:

```bash
npx skills add vibecodoor/creator -g
```

`-g` installs it user-global (e.g. `~/.claude/skills/`); omit it to scope to the current project.

**2. Ask your agent** — paste this into Claude Code / your agent:

```
Clone https://github.com/vibecodoor/creator and copy its top-level folder
(SKILL.md + templates/) into my skills directory (~/.claude/skills/creator/
for Claude Code). Then confirm the `creator` skill is available.
```

**3. Manual** — clone straight into the skills dir:

```bash
git clone https://github.com/vibecodoor/creator ~/.claude/skills/creator
```

Then start a new project and say *"create a new project: <your idea>"* — the skill triggers on new-project intent.

## How it works

1. **Clarify** — targeted questions until what / who / problem / MVP scope / constraints are clear.
2. **Plan the fan-out** — always Deep: 3 baseline research facets (competitors+market · stack+architecture · pitfalls+don't-hand-roll), plus up to 4 more for genuinely separable concerns.
3. **Research** — parallel web agents verify stack choices against current docs (via Context7), tag every claim HIGH / MEDIUM / LOW, and return distilled summaries — no raw dumps.
4. **Generate** — synthesize into `devkit/PROJECT.md` + `devkit/RESEARCH.md` + `devkit/STATE.md`, then point you at the concrete first build step.

## Folder structure

```
creator/
├── SKILL.md            # the skill: the 4-phase workflow above
└── templates/          # copy-sources for the Dev Memory Kit
    ├── PROJECT.md      # control plane (filled at init)
    ├── STATE.md        # live cursor (filled at init)
    ├── DECISIONS.md    # append-only decisions  (born on demand)
    ├── JOURNAL.md      # problem→fix log         (born on demand)
    └── specs/
        └── _template.md  # per-feature spec     (born on demand)
```

## Customizing

- **Edit `SKILL.md`** to change the research fan-out (baseline facets, agent ceiling), the confidence rubric, or what gets emitted at init.
- **Edit the `templates/`** to reshape the Dev Memory Kit — the protocol embedded in `PROJECT.md` is the canonical doctrine; keep it verbatim unless you intend to change how the kit maintains itself.
- The skill uses [Context7](https://context7.com) for live doc verification; if your agent lacks that MCP, the skill falls back to its `npx ctx7` CLI.

## Related

- **[project-memory-kit](https://github.com/vibecodoor/project-memory-kit)** — the standalone Dev Memory Kit this skill generates: the `PROJECT.md` / `STATE.md` / `DECISIONS.md` / `JOURNAL.md` / `specs/` templates plus the embedded protocol, installable on its own or usable by hand. `creator` = that kit + a deep-research bootstrap.

## Contributing

Personal project shared as a template — fork freely and adapt it to your own workflow. PRs welcome for clear improvements; open an issue first for anything large.

## License

[MIT](LICENSE) — do what you want, no warranty.
