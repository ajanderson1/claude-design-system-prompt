# Claude Design System Prompt

Reverse-engineered system prompt of Claude Design from Anthropic.

A system prompt and skill library that turns an LLM into an opinionated, accessibility-aware, AI-slop-resistant design collaborator.

Open source, MIT licensed. Install it as a **Claude Code plugin** (agent + 15 skills, one command — see [Install as a Claude Code plugin](#install-as-a-claude-code-plugin)), or drop the raw prompt into any LLM that supports system prompts (Claude, GPT, Gemini, local models) and pair with the procedural skills as needed.

## What this is

Most "design assistant" prompts produce generic SaaS-template output — aggressive gradients, emoji decoration, rounded-corner-with-left-border cards, Inter-everywhere typography. This prompt explicitly rejects those patterns and replaces them with a complete design philosophy covering:

- Content discipline (no filler — every element earns its place)
- Aesthetic discipline (avoid AI tropes, commit to a palette and tone)
- Visual hierarchy and rhythm (size, color, weight, position, density, spacing scales)
- Accessibility (WCAG, semantic HTML, keyboard navigation, focus rings, motion preferences)
- Interaction and feedback (hover, active, disabled, focus, loading, validation states)
- System thinking (components and tokens over one-off pages)
- Respecting the medium (real CSS Grid, `oklch()`, `text-wrap: pretty`, real interactive prototypes)
- Quality over quantity (depth over breadth, polish every detail)

Plus 14 procedural skills the agent can invoke for production, extraction, and review work, and a `design-workflow` front-door skill that consults you on which stages to run and then hands the work to the agent.

## What's included

```
claude-design-system-prompt/
├── .claude-plugin/                      Claude Code plugin + marketplace manifests
│   ├── plugin.json                      Plugin metadata
│   └── marketplace.json                 Marketplace catalog (one plugin, source ".")
├── agents/                              Plugin agents
│   └── design-partner.md                The design agent — full system prompt + skill awareness
├── skills/                              15 plugin skills (each <name>/SKILL.md, with frontmatter)
│   ├── design-workflow/SKILL.md         Front door — consults you, then dispatches the agent
│   ├── discovery-questions/SKILL.md
│   ├── frontend-aesthetic-direction/SKILL.md
│   └── … (12 more)
├── claude/                              Raw prompt source — Claude Code / Claude.ai variant
│   ├── system-prompt.md                 Main system prompt — 20 chapters
│   └── skills/                          15 skills as plain markdown (no frontmatter)
│       ├── design-workflow.md           Front-door workflow orchestrator
│       ├── discovery-questions.md       Kickoff question protocol
│       ├── frontend-aesthetic-direction.md  Commit to a look when no brand exists
│       ├── wireframe.md                 Low-fi exploration, 3+ variations
│       ├── make-a-deck.md               Slide presentations in HTML
│       ├── make-a-prototype.md          Interactive clickable prototype
│       ├── make-tweakable.md            Floating tweak panel
│       ├── generate-variations.md       3+ hi-fi variations across axes
│       ├── design-system-extract.md     Pull tokens from sources
│       ├── component-extract.md         Inventory reusable components
│       ├── accessibility-audit.md       WCAG, semantic, keyboard, motion
│       ├── ai-slop-check.md             Gradient / emoji / font / house-style trope detection
│       ├── hierarchy-rhythm-review.md   Size / weight / color + spacing scale
│       ├── interaction-states-pass.md   Hover / active / disabled / focus / loading
│       └── polish-pass.md               Umbrella final-gate review
├── codex/                               OpenAI Codex variant (single-loop, no subagents)
│   ├── AGENTS.md                        Codex auto-discovered entry point
│   ├── system-prompt.md                 Same prompt, adapted for Codex
│   └── skills/                          Same skills, sequential reviews instead of parallel agents
├── README.md                            This file
└── LICENSE                              MIT
```

The `agents/` + `skills/` directories and the two manifests are what Claude Code installs. The `claude/` and `codex/` directories are the raw prompt source, unchanged — use them if you'd rather paste the prompt into another tool by hand.

## Install as a Claude Code plugin

The plugin ships one agent — **`design-partner`** — that carries the full 20-chapter system prompt and knows when to invoke each of the 14 design skills, plus the 14 skills themselves and a 15th **`design-workflow`** front-door skill that consults you on which stages to run and then hands the plan to the agent.

### Install from GitHub (recommended)

In Claude Code, add this repo as a marketplace, then install the plugin:

```
/plugin marketplace add ajanderson1/claude-design-system-prompt
/plugin install claude-design-system@claude-design-system-prompt
```

Or non-interactively from your shell:

```sh
claude plugin marketplace add ajanderson1/claude-design-system-prompt
claude plugin install claude-design-system@claude-design-system-prompt
```

Then restart Claude Code (or start a new session) so the agent and skills load.

### Install from a local clone

If you've cloned the repo, point the marketplace at the local path instead:

```sh
git clone https://github.com/ajanderson1/claude-design-system-prompt
claude plugin marketplace add ./claude-design-system-prompt
claude plugin install claude-design-system@claude-design-system-prompt
```

### Use it

- **Start a guided project** with **`/design-workflow`** when the request is broad or open-ended ("help me design X end to end"). It reads your brief, asks which stages you want (discovery, aesthetic direction, wireframe, build, variations, review/polish), then dispatches the agent to run exactly those — one consultation, then autonomous.
- **Invoke the agent directly** by describing design work — "design a landing page for X", "build me a clickable prototype", "this UI looks AI-generated, fix it". Claude routes substantive design requests to the `design-partner` agent, or you can call it explicitly with `@claude-design-system:design-partner`. The agent runs the design philosophy and reaches for the skills as triggers match.
- **Invoke a single skill directly** when you want just one procedure — e.g. `/accessibility-audit`, `/make-a-deck`, `/polish-pass`. All the skills are available on their own, and the agent chains them for you when it drives the work.

### Manage or remove it

```sh
claude plugin list                                          # see what's installed
claude plugin uninstall claude-design-system@claude-design-system-prompt
claude plugin marketplace remove claude-design-system-prompt
```

> **Note on the system prompt.** A Claude Code plugin can't replace the base system prompt, so the design philosophy is delivered *through the agent* — when you invoke `design-partner`, it operates under the full prompt. To make it always-on for a session regardless of routing, paste `claude/system-prompt.md` into an output style or your project's `CLAUDE.md`. The skills work the same way either route.

## How to use it (any LLM)

### Use the system prompt directly

Paste the contents of `system-prompt.md` as the system prompt for any LLM that supports them. The agent will follow the design philosophy and reference the skills by name when tasks match.

### Use the skills as procedures

Each skill in `skills/` is a self-contained, phased procedure. The skill name is the trigger — when the user's request matches a skill description, the agent loads that skill and follows it.

Skills group into three categories:

**Production** — build something
`discovery-questions` · `frontend-aesthetic-direction` · `wireframe` · `make-a-deck` · `make-a-prototype` · `make-tweakable` · `generate-variations`

**System** — extract structure
`design-system-extract` · `component-extract`

**Review** — audit and fix
`accessibility-audit` · `ai-slop-check` · `hierarchy-rhythm-review` · `interaction-states-pass` · `polish-pass`

Skills can be chained. A typical greenfield flow:

```
discovery-questions → frontend-aesthetic-direction → wireframe → make-a-prototype → polish-pass
```

A brand-aware flow:

```
design-system-extract → generate-variations → make-tweakable → polish-pass
```

### Adapt for your platform

The prompt assumes an HTML-output design environment (similar to Claude.ai's design tool). If your target environment is different — a Figma plugin, a code-only assistant, a chat-only design coach — you'll need to adjust the workflow chapters and tool references. The principles (chapters 5–16) translate to any medium.

## Model calibration

The `claude/` variant is calibrated for current Anthropic frontier models (Fable 5 and the Opus 4.7/4.8 lineage), which follow instructions more literally and need less aggressive prompting than earlier generations:

- **Conditions instead of quotas.** No "ask at least N questions", no "CRITICAL: YOU MUST". Current models treat quotas as literal contracts and over-trigger on them; the prompt states the conditions under which to act, plus an autonomy clause for minor decisions (pick a reasonable option and note it, rather than asking).
- **Explicit triggers for skills and subagents.** These models under-reach for optional capabilities by default, so every skill description states *when* to invoke it, and verifier delegation has an explicit trigger ("after every substantive visual change").
- **Coverage-first reviews.** Review agents report everything with confidence/severity estimates and let the aggregation step filter. Current models follow "only report important issues" literally, which silently suppresses findings.
- **House-style guard.** The current models' default aesthetic (cream background, serif display type, terracotta/amber accents) is detected by `ai-slop-check` (rule 9) and pre-empted by `frontend-aesthetic-direction`'s four-directions protocol. Sampling parameters (`temperature`) no longer exist on these models, so visual variety must come from explicit per-variation specs, not randomness.

On older models (Claude Opus/Sonnet 4.6 and earlier, or non-Anthropic models), the calmer phrasing may under-trigger — restore stronger imperative language if you see the model skipping question rounds or reviews. The `codex/` variant is maintained separately and is unaffected by these notes.

## Design principles, in short

The 20 chapters in `system-prompt.md` cover:

| #   | Chapter                                   |
| --- | ----------------------------------------- |
| 1   | Identity and role                         |
| 2   | Workflow                                  |
| 3   | Asking questions first                    |
| 4   | Rooting designs in existing context       |
| 5   | Content principles — no filler            |
| 6   | Aesthetic principles — purposeful visuals |
| 7   | Visual hierarchy and rhythm               |
| 8   | Typography system                         |
| 9   | Color system                              |
| 10  | Accessibility and inclusivity             |
| 11  | Interaction and feedback                  |
| 12  | Simplicity and one clear CTA              |
| 13  | System thinking                           |
| 14  | Respecting the medium                     |
| 15  | Understanding users                       |
| 16  | Quality over quantity                     |
| 17  | Output principles                         |
| 18  | Collaboration and delivery                |
| 19  | IP and content boundaries                 |
| 20  | Available skills                          |

## Contributing

Issues and PRs welcome. Particularly useful contributions:

- Additional review skills (e.g., copy review, motion review, dark-mode parity check)
- Adapted prompts for other environments (Figma, code-only, terminal-only)
- Real-world failure cases the prompt should defend against
- Translations of the prompt into other languages

Please keep the same operational tone and avoid bloating the prompt — every chapter should earn its place, the same standard the prompt holds the agent to.

## License

MIT — see `LICENSE`.

You can use, modify, and distribute this prompt and skill library for any purpose, including commercial use. No attribution required, but appreciated.
