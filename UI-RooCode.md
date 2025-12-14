# DrySpec in RooCode

This document explains how to use DrySpec from RooCode using modes and slash-commands.

## Available modes

- `DrySpec` — Agile specification coach that understands the DrySpec framework. It focuses on FR/NFR/Glossary/Architecture/Subsystem specs as defined in your project’s Specification index (by default under `docs/specs/`), keeps them short and dense, and follows the Git/context rules from the Constitution.
- `Ask` (built-in) — Large‑context mode that reads and summarizes sizable chunks of code or diffs. The agent uses it from sub‑tasks when it needs broad code context, then brings back concise findings to `DrySpec`.

## Available slash-commands

- `/dryspec-init-existing` — Brownfield initialization. Guides you through bootstrapping DrySpec specs for an existing codebase: checking Git state, identifying subsystems, harvesting observations in sub‑tasks, and drafting initial Architecture/FR/NFR/Glossary specs.
- `/dryspec-init-new` — Greenfield initialization. Helps you refine the Constitution for a new project and create the initial FR/NFR/Glossary/Architecture/Subsystem spec skeletons ready for the Specification‑Driven Change workflow.
- `/dryspec-change` — Specification‑Driven Change. Starting from a desired change, it drives the FR/NFR/Glossary updates, then Architecture/Subsystems updates, stages the spec changes, and runs a critique pass before handing off to a Coder agent.
- `/dryspec-reconcile` — Code‑Driven Change. Starting from staged code changes, it explores the diff in sub‑tasks, compares it to the existing specs, and guides you in reconciling specs and code.

Each command:

- Keeps a lightweight TODO list of the main workflow steps using RooCode's `update_todo_list` tool, updating it as the agent makes progress or the plan changes.
- Automatically switches between the DrySpec mode (for specs) and the built‑in Ask mode (for code deep dives).
- Uses short‑lived sub‑tasks (shown in the UI as "in a sub‑task") whenever it needs a fresh, focused context.
- Encourages minimal, step‑scoped contexts and Git‑based protection (staging and explicit user approval for Git operations).

## Sample sessions

### Brownfield: `/dryspec-init-existing`

1. Outside RooCode chat: Make sure your Constitution (for example in `AGENTS.md`/`CLAUDE.md`) exists and roughly describes the project and code best practices.
2. In the main RooCode chat: run `/dryspec-init-existing`.
3. DrySpec checks `git status` and verifies that a Constitution is present; if it is missing or obviously incomplete, it asks you to pause and fix it before proceeding.
4. DrySpec (in a sub‑task, Ask mode) reads and summarizes the code in a chosen subsystem directory, capturing responsibilities, external behaviors, and candidate FR/NFR/Glossary/Architecture notes in a short, dense list.
5. Back in the main chat, DrySpec turns those notes into concise updates for your Architecture and Subsystem specs (for example `docs/specs/architecture.md` and `docs/specs/sub-*.md` in the default layout) and initial FR/NFR/Glossary specs (for example `docs/specs/fr-*.md`/`docs/specs/nfr.md`/`docs/specs/glossary.md`), suggests staging the spec files, and may open another sub‑task in DrySpec mode to critique the staged specs.

### Greenfield: `/dryspec-init-new`

1. In the main RooCode chat: run `/dryspec-init-new` to start the Greenfield helper.
2. DrySpec confirms that this is a new project, asks a few clarifying questions, and helps you draft the Constitution text (one‑paragraph description plus code best practices).
3. Outside chat: copy the proposed Constitution into `AGENTS.md`/`CLAUDE.md` (or equivalent) and save it so it is always loaded.
4. DrySpec (in a sub‑task, DrySpec mode) creates short, skeletal FR/NFR/Glossary/Architecture/Subsystem entries in the locations defined by your Specification index (for example `docs/specs/fr-*.md`, `docs/specs/nfr.md`, `docs/specs/glossary.md`, and `docs/specs/architecture.md`/`docs/specs/sub-*.md` in the default layout) that respect DrySpec limits and leave detailed design to later.
5. Back in the main chat, DrySpec summarizes what was written, suggests staging the new spec files, and reminds you to use `/dryspec-change` for the first real feature change.

### Specification‑Driven Change: `/dryspec-change`

1. In the main RooCode chat: run `/dryspec-change` and briefly describe the desired change (for example, `/dryspec-change` — "Add SSO login, keep existing signup flow intact").
2. DrySpec checks for a clean Git state, then asks targeted clarifying questions about the change and which parts of the system it affects.
3. DrySpec (in a sub‑task, DrySpec mode) updates the relevant FRs and, if needed, NFRs and Glossary entries, keeping each spec file small, cohesive, and free of UI copy or data‑model details.
4. DrySpec (in another sub‑task, DrySpec mode) updates Architecture and Subsystem specs to reflect the same change, using `git diff` on the spec files from the previous step as context.
5. Back in the main chat, DrySpec proposes staging the updated spec files, runs a critique sub‑task to review them for violations of DrySpec rules, and summarizes the final spec delta so you can hand off to a Coder agent with the staged specs.

### Code‑Driven Change: `/dryspec-reconcile`

1. Outside chat: stage only the relevant code changes (for example, with `git add` on source files but not specs).
2. In the main RooCode chat: run `/dryspec-reconcile` to reconcile the staged code with the specs.
3. DrySpec verifies that only code (no spec files) is staged and asks which feature or bug the changes are meant to address.
4. DrySpec (in a sub‑task, Ask mode) reads and summarizes the staged diff and nearby code using its large context, capturing behavior changes and potential impacts on existing FR/NFR/Architecture/Subsystem specs.
5. DrySpec (in a sub‑task, DrySpec mode) proposes minimal, destructive edits to the affected spec files so that they match the staged code behavior, then in the main chat suggests staging the spec changes separately, runs a quick critique sub‑task, and summarizes whether to adjust code, adjust specs, or split the work.
