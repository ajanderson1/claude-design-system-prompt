# Design Workflow: Guided Front Door

Orchestrate a design project by first agreeing the plan with the user, then running the agreed stages yourself. **The point is to run only the stages the work needs** — a small tweak doesn't need discovery and four reviews; a greenfield brand build does. You consult once, then execute the plan end to end.

Codex runs as a single agent loop — you *are* the design agent (see `system-prompt.md`). There is no separate agent to dispatch to; "dispatch" here means running the chosen skills in sequence yourself, in this loop.

## Phase 1: Read the request and any attached context

Read the brief and every attached resource — codebase, screenshots, brand guide, existing HTML, linked design system. What you find changes which stages are worth running (an attached brand guide makes `frontend-aesthetic-direction` redundant; an attached codebase makes `design-system-extract` valuable). Note the medium (page / deck / prototype / dashboard / mobile) and whether this is greenfield or rooted in existing context.

## Phase 2: Consult the user on which stages to run

Ask the user a single consolidated question: *"Which parts of the design workflow do you want to run?"* Present the stages below as a checklist and recommend the ones this specific brief needs, with a one-line reason each, so the user chooses from an informed default rather than a blank list. Use the harness's question/form mechanism if one exists; otherwise ask inline as a numbered list and stop for the reply.

The stages, in canonical order:

1. **Discovery questions** (`discovery-questions`) — a kickoff question round to lock scope, fidelity, audience, and constraints. Recommend when the brief is open or ambiguous.
2. **Aesthetic direction** (`frontend-aesthetic-direction`) — propose 4 visual directions and commit to one. Recommend for greenfield hi-fi work with no brand to match.
3. **Extract design context** (`design-system-extract` / `component-extract`) — pull tokens and reusable components from a brand, codebase, or screenshots. Recommend when existing context is attached.
4. **Wireframe** (`wireframe`) — low-fi, greyscale, disposable layout exploration. Recommend when the layout or flow is unsettled.
5. **Build** (`make-a-prototype` / `make-a-deck` / build a page) — the real hi-fi deliverable. Almost always wanted; pick the build skill that fits the medium.
6. **Variations** (`generate-variations`) — 3+ distinct hi-fi options across substantive axes. Recommend when the user wants choices.
7. **Make tweakable** (`make-tweakable`) — a floating panel to adjust the finished design live. Recommend when the user will want to iterate on colors/copy/layout themselves.
8. **Review + polish** (`polish-pass`, or the narrower review skills) — the pre-ship quality gate. Recommend before any delivery.

Also offer, as escape hatches:
- **"Decide for me"** — you scope and run the workflow yourself, without a fixed stage list.
- **"Just build it"** — skip discovery and review; go straight to the build.

Keep this to one round. If the request is so specific that a single skill obviously covers it, say so and offer to just run that skill instead of the full consultation.

## Phase 3: Confirm the plan

Echo back the chosen stages as an ordered plan in one line — e.g. *"Plan: discovery → aesthetic direction → build → polish."* If the user picked stages in an order that fights the canonical sequence (e.g. review before build), reorder to the sensible order and note it. If they picked nothing buildable (only reviews, but there's nothing built yet), flag it and adjust.

## Phase 4: Run the agreed stages, in order

Work through the plan one stage at a time, invoking each backing skill by name and carrying its output into the next stage. This is a single continuous loop — finish each stage before starting the next, and don't stop to ask the user about minor decisions mid-flow; make defensible calls and note them. Return to the user only for a blocker or a genuinely direction-changing question.

If the user chose **"Decide for me,"** scope the workflow yourself from the brief and context, then run it under the same autonomy contract.

## Phase 5: Summarize the result

When the plan is complete, report concisely — the deliverable(s) produced, open decisions flagged for sign-off, and anything left out of scope. If the user wants changes, that's a fresh, narrower request (often a single skill).

## Anti-patterns

- **Don't skip the consultation** and run a full greenfield workflow on a request that wanted one tweak. The whole point of this skill is running only the needed stages.
- **Don't stop between every stage** to check in. Consult once up front, then execute the agreed plan end to end.
- **Don't pad the plan.** A stage whose output wouldn't change the deliverable is noise — leave it out even if it's "standard."
