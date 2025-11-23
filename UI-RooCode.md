# LeanSDD in RooCode

This document explains how to use LeanSDD from RooCode using modes and slash-commands.

## Available modes

- `LeanSDD` — Agile specification coach that understands the LeanSDD framework. It focuses on FR/NFR/Glossary/Architecture/Subsystem specs under `docs/`, keeps them short and dense, and follows the Git/context rules from the Constitution.
- `Ask` (built-in) — Large‑context code reader for deep dives into the codebase. The agent uses it from sub‑tasks when it needs to understand existing code or diffs, then bring back concise findings to `LeanSDD`.

## Available slash-commands

- `/lsdd-init-existing` — Brownfield initialization. Guides you through bootstrapping LeanSDD specs for an existing codebase: checking Git state, identifying subsystems, harvesting observations in sub‑tasks, and drafting initial Architecture/FR/NFR/Glossary specs.
- `/lsdd-init-new` — Greenfield initialization. Helps you refine the Constitution for a new project and create the initial FR/NFR/Glossary/Architecture/Subsystem spec skeletons ready for the Specification‑Driven Change workflow.
- `/lsdd-change` — Specification‑Driven Change. Starting from a desired change, it drives the FR/NFR/Glossary updates, then Architecture/Subsystems updates, stages the spec changes, and runs a critique pass before handing off to a Coder agent.
- `/lsdd-reconcile` — Code‑Driven Change. Starting from staged code changes, it explores the diff in sub‑tasks, compares it to the existing specs, and guides you in reconciling specs and code.

Each command:

- Automatically switches between the LeanSDD mode (for specs) and the built‑in Ask mode (for code deep dives).
- Uses short‑lived sub‑tasks (shown in the UI as "in a sub‑task") whenever it needs a fresh, focused context.
- Encourages minimal, step‑scoped contexts and Git‑based protection (staging and explicit user approval for Git operations).

## Sample sessions

### Brownfield: `/lsdd-init-existing`

1. Outside RooCode chat: Make sure your Constitution (for example in `AGENTS.md`/`CLAUDE.md`) exists and roughly describes the project and code best practices.
2. In the main RooCode chat: run `/lsdd-init-existing`.
3. LeanSDD checks `git status` and verifies that a Constitution is present; if it is missing or obviously incomplete, it asks you to pause and fix it before proceeding.
4. LeanSDD (in a sub‑task, Ask mode) deep‑dives into a chosen subsystem directory, summarizing responsibilities, external behaviors, and candidate FR/NFR/Glossary/Architecture notes in a short, dense list.
5. Back in the main chat, LeanSDD turns those notes into concise updates for `docs/architecture.md`, `docs/sub-*.md`, and initial `docs/fr-*.md`/`docs/nfr.md`/`docs/glossary.md`, suggests staging the spec files, and may open another sub‑task in LeanSDD mode to critique the staged specs.

### Greenfield: `/lsdd-init-new`

1. In the main RooCode chat: run `/lsdd-init-new` to start the Greenfield helper.
2. LeanSDD confirms that this is a new project, asks a few clarifying questions, and helps you draft the Constitution text (one‑paragraph description plus code best practices).
3. Outside chat: copy the proposed Constitution into `AGENTS.md`/`CLAUDE.md` (or equivalent) and save it so it is always loaded.
4. LeanSDD (in a sub‑task, LeanSDD mode) creates short, skeletal `docs/fr-*.md`, `docs/nfr.md`, `docs/glossary.md`, and `docs/architecture.md`/`docs/sub-*.md` entries that respect LeanSDD limits and leave detailed design to later.
5. Back in the main chat, LeanSDD summarizes what was written, suggests staging the new spec files, and reminds you to use `/lsdd-change` for the first real feature change.

### Specification‑Driven Change: `/lsdd-change`

1. In the main RooCode chat: run `/lsdd-change` and briefly describe the desired change (for example, `/lsdd-change` — "Add SSO login, keep existing signup flow intact").
2. LeanSDD checks for a clean Git state, then asks targeted clarifying questions about the change and which parts of the system it affects.
3. LeanSDD (in a sub‑task, LeanSDD mode) updates the relevant FRs and, if needed, NFRs and Glossary entries, keeping each spec file small, cohesive, and free of UI copy or data‑model details.
4. LeanSDD (in another sub‑task, LeanSDD mode) updates Architecture and Subsystem specs to reflect the same change, using `git diff` on the spec files from the previous step as context.
5. Back in the main chat, LeanSDD proposes staging the updated spec files, runs a critique sub‑task to review them for violations of LeanSDD rules, and summarizes the final spec delta so you can hand off to a Coder agent with the staged specs.

### Code‑Driven Change: `/lsdd-reconcile`

1. Outside chat: stage only the relevant code changes (for example, with `git add` on source files but not specs).
2. In the main RooCode chat: run `/lsdd-reconcile` to reconcile the staged code with the specs.
3. LeanSDD verifies that only code (no spec files) is staged and asks which feature or bug the changes are meant to address.
4. LeanSDD (in a sub‑task, Ask mode) uses a large context to inspect the staged diff and nearby code, summarizing behavior changes and potential impacts on existing FR/NFR/Architecture/Subsystem specs.
5. LeanSDD (in a sub‑task, LeanSDD mode) proposes minimal, destructive edits to the affected spec files so that they match the staged code behavior, then in the main chat suggests staging the spec changes separately, runs a quick critique sub‑task, and summarizes whether to adjust code, adjust specs, or split the work.
