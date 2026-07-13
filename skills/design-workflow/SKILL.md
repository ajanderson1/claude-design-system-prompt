---
name: design-workflow
description: Use as the front door to a design project — "run the design workflow", "start a design project", "help me design X end to end", "walk me through designing this". Consults the user on which parts of the design workflow they want (discovery, aesthetic direction, wireframe, build, variations, review/polish), then dispatches the design-partner agent to run exactly those stages, in order. Invoke when the request is broad or open-ended enough that the right sequence of stages isn't obvious; for a single well-scoped task (just a deck, just an audit) call that skill directly instead.
---

# Design Workflow: Guided Front Door

Orchestrate a design project by first agreeing the plan with the user, then handing the work to the `design-partner` agent. **The point is to run only the stages the work needs** — a small tweak doesn't need discovery and four reviews; a greenfield brand build does. You consult once, then the agent executes autonomously.

Do not design anything yourself in this skill. Your job is to scope the workflow with the user and dispatch the agent.

## Phase 1: Read the request and any attached context

Read the brief and every attached resource — codebase, screenshots, brand guide, existing HTML, linked design system. What you find changes which stages are worth running (an attached brand guide makes `frontend-aesthetic-direction` redundant; an attached codebase makes `design-system-extract` valuable). Note the medium (page / deck / prototype / dashboard / mobile) and whether this is greenfield or rooted in existing context.

## Phase 2: Consult the user on which stages to run

Use the `AskUserQuestion` tool with a single **multi-select** question: *"Which parts of the design workflow do you want to run?"* Offer the stages below as options, pre-reasoned for this specific request — recommend the ones the brief needs and say why in each option's description, so the user is choosing from an informed default rather than a blank checklist.

The stages, in canonical order:

1. **Discovery questions** (`discovery-questions`) — a kickoff question round to lock scope, fidelity, audience, and constraints. Recommend when the brief is open or ambiguous.
2. **Aesthetic direction** (`frontend-aesthetic-direction`) — propose 4 visual directions and commit to one. Recommend for greenfield hi-fi work with no brand to match.
3. **Extract design context** (`design-system-extract` / `component-extract`) — pull tokens and reusable components from a brand, codebase, or screenshots. Recommend when existing context is attached.
4. **Wireframe** (`wireframe`) — low-fi, greyscale, disposable layout exploration. Recommend when the layout or flow is unsettled.
5. **Build** (`make-a-prototype` / `make-a-deck` / build a page) — the real hi-fi deliverable. Almost always wanted; the agent picks the right build skill for the medium.
6. **Variations** (`generate-variations`) — 3+ distinct hi-fi options across substantive axes. Recommend when the user wants choices.
7. **Make tweakable** (`make-tweakable`) — a floating panel to adjust the finished design live. Recommend when the user will want to iterate on colors/copy/layout themselves.
8. **Review + polish** (`polish-pass`, or the narrower review skills) — the pre-ship quality gate. Recommend before any delivery.

Also include, as escape hatches:
- **"Let the agent decide"** — dispatch with no fixed stage list; the agent scopes and runs the workflow itself.
- **"Just build it"** — skip discovery and review; go straight to the build.

Keep the question to this one round. If the request is so specific that a single skill obviously covers it, say so and offer to just run that skill instead of the full consultation.

## Phase 3: Confirm the plan

Echo back the chosen stages as an ordered plan in one line — e.g. *"Plan: discovery → aesthetic direction → build → polish."* If the user picked stages in an order that fights the canonical sequence (e.g. review before build), reorder to the sensible order and note it. If they picked nothing buildable (only reviews, but there's nothing built yet), flag it and adjust.

## Phase 4: Dispatch the design-partner agent

Launch the `design-partner` agent with the `${AGENT_TOOL_NAME}` tool (scoped name `claude-design-system:design-partner`). Pass it, explicitly:

- **The brief** and the paths to any attached context.
- **The agreed stage list, in order**, with the instruction to run *only* those stages and the skill that backs each. Tell it to invoke each backing skill by name via its Skill tool rather than reimplementing it.
- **The autonomy contract:** run the stages end to end without returning for minor decisions; make defensible calls and note them. Return to this conversation only for a blocker or a genuinely direction-changing question.
- If the user chose **"Let the agent decide,"** dispatch with the brief and context and let the agent scope the workflow itself, subject to the same autonomy contract.

One dispatch runs the whole agreed sequence — don't dispatch a separate agent per stage. The agent already knows the philosophy and how to chain the skills; you are handing it a scoped plan, not micromanaging each step.

## Phase 5: Relay the result

When the agent returns, relay its summary to the user concisely — the deliverable(s) it produced, open decisions it flagged for sign-off, and anything left out of scope. Don't re-run the work or re-review it here; if the user wants changes, that's a fresh, narrower request (often a single skill).

## Anti-patterns

- **Don't skip the consultation** and dispatch a full greenfield workflow on a request that wanted one tweak. The whole point of this skill is running only the needed stages.
- **Don't design in this skill.** If you find yourself writing HTML, you've bypassed the agent — stop and dispatch.
- **Don't dispatch per stage.** One agent runs the agreed sequence; multiple dispatches lose the thread between stages.
- **Don't pad the plan.** A stage whose output wouldn't change the deliverable is noise — leave it out even if it's "standard."
