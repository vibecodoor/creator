---
name: creator
description: "Initialize a new project with deep research and generate ready-to-develop project files. Use when the user wants to start a new project, says 'new project', 'create project', 'I want to build X', or describes a product/app idea. Also trigger when the user mentions project initialization, kickoff, or bootstrapping a new codebase."
---

# Project Creator

Research-driven project initialization: clarify → plan fan-out → always-Deep web research (≥3 parallel agents) → generate the Dev Memory Kit files.

The goal is to give the user a clear, actionable foundation so they can start coding immediately — not a wall of text they need to re-read and re-decide.

Output is the **Dev Memory Kit** layout (a thin always-loaded control plane + on-demand surfaces), not an ad-hoc brief. Templates are bundled in `templates/` next to this skill. Core doctrine: **never pre-create empty stubs** — at init you emit only the surfaces that already have content; the rest are born on triggers (the protocol embedded in `PROJECT.md` governs that).

**All kit files live in a `devkit/` folder at the project root** — `devkit/PROJECT.md`, `devkit/RESEARCH.md`, `devkit/STATE.md`, and (when their triggers fire) `devkit/DECISIONS.md`, `devkit/JOURNAL.md`, `devkit/specs/`. This keeps dev-memory files from mixing with the project's own code/docs. Create `devkit/` if it doesn't exist. All surfaces are colocated there, so the relative links inside the templates (`[STATE.md](STATE.md)`, `specs/…`) stay valid unchanged — do not rewrite them.

## 1. Clarify the Idea

Before any research, make sure you understand what the user wants to build. Use askuserquestion tool to ask targeted follow-ups until you have:

- **What** the product does and **who** it's for
- **What problem** it solves (not just features — the underlying need)
- **MVP scope** — minimum features for a usable first version
- **Constraints** — timeline, budget, solo dev, tech preferences, platforms

Don't over-ask if the user already gave enough detail. But don't start research on a vague idea — garbage in, garbage out.

If you think the user's approach is suboptimal or risky, say so and propose a better direction before proceeding.

## 2. Plan the research fan-out (always Deep)

This skill **always runs Deep Research — never scale down**. Every run spawns **at least 3 web-research agents, in parallel**, and adds more when the idea has extra separable concerns. The only decision here is *how many beyond 3* and *what each one owns* — **never fewer than 3 web agents.**

**Always spawn these 3 baseline web facets** (one agent each, non-overlapping):

1. **Competitors + market** — what exists, pricing, gaps to exploit, audience size, willingness to pay, distribution.
2. **Stack + architecture + references** — best technical approach, proven patterns, stack choices verified against current docs via Context7, plus open-source repos / reference architectures worth studying.
3. **Pitfalls + don't-hand-roll** — what kills projects like this, time-wasters, problems where a library always beats custom code (auth, payments, email, uploads…).

**Add one more agent per genuinely separable concern** (only when it won't overlap the three above), e.g.:

- a distinct subsystem that deserves its own deep-dive — payments engine · realtime layer · AI / agents core · data pipeline;
- a regulated dimension — privacy / legal / compliance (health, finance, PII);
- a dedicated business-model / GEO / monetization deep-dive.

**Ceiling: 7 web agents.** Between 4 and 7 the only rule is *non-overlap* — never spawn two agents that would research the same thing. State the planned count and each agent's facet to the user in one line before spawning (e.g. *"Deep: 4 web — 3 baseline + a payments-subsystem deep-dive"*). Proceed without a blocking question.

## 3. Research: Web + Docs

Spawn the **general-purpose** web subagents chosen in step 2 (**3 or more**) **in parallel** — issue all Agent calls in a single message. Give **each** agent:
- The project brief from step 1
- **Its specific facet** from the step-2 split, with an explicit instruction to stay in its lane and return only that facet's findings (so agents don't overlap or duplicate work)
- **An output budget**: each agent must return a **distilled, structured summary** — decisions, evidence, confidence tags, and source links only. **No raw page/search dumps.** You synthesize these summaries yourself in step 4, so what each agent hands back must already be clean enough to drop into `RESEARCH.md` with light editing. This keeps the parallel fan-out from flooding the main context (the same lean-context discipline the kit enforces).

The targets below are the menu the three baseline facets are carved from; any extra agent owns one separable concern beyond them. The research philosophy, Context7 usage, confidence levels, and the pre-submission checklist below **apply to every agent**.

**Research philosophy — training data is a hypothesis:**
> Your knowledge is 6-18 months stale. Don't assert capabilities, versions, or best practices without checking current sources. "I'm confident" means nothing — verify or flag as unverified.

Research targets:

- **Competitors & similar products** — what exists, pricing, gaps the user can exploit
- **Best technical approaches** — architecture, stack choices, proven patterns for this type of app
- **Implementation examples** — open-source repos, reference architectures worth studying
- **Market context** — target audience size, willingness to pay, distribution channels
- **Common pitfalls** — what kills projects like this, what wastes time
- **Don't Hand-Roll** — problems where custom solutions are always worse than libraries (auth, payments, email, file uploads, etc.)

**Use Context7 MCP** (`mcp__plugin_context7_context7__resolve-library-id` → `mcp__plugin_context7_context7__query-docs`) to pull up-to-date documentation for any recommended libraries or frameworks. This catches deprecated APIs, breaking changes, and outdated patterns that training data misses.

**Context7 CLI fallback** — if MCP tools are unavailable to the subagent, use CLI:
```bash
npx --yes ctx7@latest library <name> "<query>"
npx --yes ctx7@latest docs <libraryId> "<query>"
```
Do not skip doc verification because MCP tools failed.

**Confidence levels — mark every factual claim:**

| Level | Source | How to present |
|-------|--------|----------------|
| HIGH | Context7, official docs | State as fact |
| MEDIUM | WebSearch + confirmed by official source | State with attribution |
| LOW | WebSearch only, single source, training data only | Flag: "needs validation" |

Investigation, not confirmation: gather evidence first, form conclusions from evidence. Don't find articles supporting your initial guess.

**Key principle — be prescriptive:**
> "Use X because Y" — not "Consider X or Y or Z."
> The user needs decisions, not a menu. If there's a clearly better option, recommend it. Flag genuine tradeoffs only when they actually matter.

**The question driving research is not** "which library should I use?" **It's:** "What do I not know that I don't know?" — hidden complexity, non-obvious integration issues, things that seem simple but aren't.

**Pre-submission checklist (run before returning results):**
- [ ] Stack recommendations verified against current docs (not just training data)
- [ ] Negative claims ("X can't do Y") confirmed with official source
- [ ] Multiple sources for critical stack decisions
- [ ] Publication dates checked — prefer current year
- [ ] Gaps honestly flagged as LOW confidence, not hidden

## 4. Generate the Dev Memory Kit files

**You (the skill, in the main thread) do the synthesis yourself — do not spawn a separate synthesis agent.** Working from the agents' bounded summaries (step 3), merge **every** web-agent output — reconcile overlaps, drop duplicates, and surface any conflicts with their confidence tags (prefer HIGH/Context7 > MEDIUM > LOW; if still tied on a load-bearing choice, put it to the user) — then lay down the kit in the **`devkit/` folder at the project root** (create it if absent) using the bundled templates in `templates/` (next to this skill). **Copy each template, then fill its `{{placeholders}}` from the research** — do not invent a structure, and do not leave placeholders unfilled.

### What to emit at init (and only this)

Doctrine = **never pre-create empty stubs**. At init you have real content for exactly three surfaces, so emit only these (all inside `devkit/`):

1. **`devkit/PROJECT.md`** ← `templates/PROJECT.md` — the control plane. Fill: Project Card, What This Is (vision), Core Value, User & Problem, Core Loop, Scope (Active = MVP features · Out of Scope = non-goals), Success Criteria, Architecture (modules/data flow), Invariants, **Plan** (ordered build steps), **Post-MVP**, **Launch Checklist**, and the inline **Decisions** digest (seed it with the 1-3 load-bearing choices from research). Leave the **Memory Map & Routing** table as-is (it's static boilerplate — only add a code-dir row if a path is genuinely non-obvious). Use **Canonical References** only for external links, if any. Keep the embedded **Dev Memory Protocol verbatim** — it is the project's self-bootstrapping manual.
2. **`devkit/RESEARCH.md`** ← see schema below — the evidence base, read once for decisions then rarely revisited. Referenced from PROJECT.md's Canonical References.
3. **`devkit/STATE.md`** ← `templates/STATE.md` — seed `## Now` with the concrete first build step and a `Recent:` line ("project initialized from research"), and `## Next` with steps 2-3 from the Plan. (Justified at init: the work spans sessions from day one.)

**Do NOT create** `devkit/DECISIONS.md`, `devkit/JOURNAL.md`, or any `devkit/specs/*.md` at init. They are born on their triggers (3rd recorded decision · first non-trivial problem · a feature that outlives one session). When a trigger fires, copy the matching file from `templates/` (`DECISIONS.md`, `JOURNAL.md`, `specs/_template.md`) into `devkit/` — that is the canonical copy-source. The embedded protocol already tells the agent this; you are just honoring it at init.

> If the user explicitly asked for a different init set (e.g. also seed `specs/mvp.md` with the ordered plan + acceptance criteria), follow that — but never lay down empty stubs. A spec is allowed at init **only** if you fill it from the research.

### `RESEARCH.md` schema

Reference material — read once for decisions, then rarely revisited.

```markdown
# {Project Name} — Research

## Competitor Analysis
<!-- What exists, pricing, strengths, weaknesses, gaps to exploit -->

## Recommended Stack
<!-- Each technology choice with WHY it was chosen over alternatives -->
<!-- Informed by current best practices verified against docs -->
<!-- Mark confidence: [HIGH], [MEDIUM], or [LOW] for each choice -->

## Architecture Patterns
<!-- Proven approaches for this type of project — specific patterns, not abstract advice -->

## Don't Hand-Roll
<!-- Problems where custom code is always worse than a library -->
<!-- Format: Problem → Use this → Why not custom -->

## Anti-features (v1)
<!-- Things that seem useful but hurt MVP — explicitly do NOT build these -->

## Risks & Pitfalls
<!-- From web research -->
<!-- Concrete, not generic ("X library has a known issue with Y" > "be careful with dependencies") -->

## Key Resources
<!-- Links to docs, repos, examples worth studying -->
```

### After generating

Give a brief summary of what was laid down (`devkit/PROJECT.md` + `devkit/RESEARCH.md` + `devkit/STATE.md`), explicitly note that DECISIONS/JOURNAL/specs are deferred until their triggers (so the user isn't surprised they're missing), and suggest the concrete first step to start building — which should match `STATE.md`'s `## Now`.
