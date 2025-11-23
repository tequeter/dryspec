# LeanSDD in RooCode

This document explains how to use LeanSDD from RooCode using modes and slash-commands.

## Available modes

- `LeanSDD` — Agile specification coach that understands the LeanSDD framework. It focuses on FR/NFR/Glossary/Architecture/Subsystem specs under `docs/`, keeps them short and dense, and follows the Git/context rules from the Constitution.
- `Ask` (built-in) — Large‑context code reader for deep dives into the codebase. The agent uses it from sub‑tasks when it needs to understand existing code or diffs, then bring back concise findings to `LeanSDD`.

## Available slash-commands

- `/lsdd-init-existing` — Brownfield initialization. Guides you through bootstrapping LeanSDD specs for an existing codebase: checking Git state, identifying subsystems, harvesting observations with `ask` in sub‑tasks, and drafting initial Architecture/FR/NFR/Glossary specs.
- `/lsdd-init-new` — Greenfield initialization. Helps you refine the Constitution for a new project and create the initial FR/NFR/Glossary/Architecture/Subsystem spec skeletons ready for the Specification‑Driven Change workflow.
- `/lsdd-change` — Specification‑Driven Change. Starting from a desired change, it drives the FR/NFR/Glossary updates, then Architecture/Subsystems updates, stages the spec changes, and runs a critique pass before handing off to a Coder agent.
- `/lsdd-reconcile` — Code‑Driven Change. Starting from staged code changes, it uses `ask` to understand the diff, compares it to the existing specs, and guides you in reconciling specs and code.

Each command:

- Uses `switch_mode` to select the appropriate mode (`LeanSDD` for specs, `ask` for deep dives).
- Uses `new_task` when it needs a fresh, focused context (shown in the UI as "in a sub‑task").
- Encourages minimal, step‑scoped contexts and Git‑based protection (staging and explicit user approval for Git operations).

## Sample sessions

### Brownfield: `/lsdd-init-existing`

- User (main chat): `/lsdd-init-existing`
- LeanSDD: Checks `git status`, asks which parts of the repo to focus on, and proposes a list of subsystems based on top‑level directories.
- LeanSDD (in a sub‑task, `ask` mode): Deep‑dives into one subsystem directory, summarizing responsibilities, external behaviors, and potential FR/NFR/Glossary/Architecture notes in a short, dense bullet list.
- LeanSDD (main chat): Uses the returned notes to propose concise updates to `docs/architecture.md`, `docs/sub-*.md`, and initial `docs/fr-*.md`/`docs/nfr.md`/`docs/glossary.md`, then suggests staging the spec files.
- LeanSDD (in a sub‑task, `LeanSDD` mode): Runs a critique pass on the staged spec changes, checking concision, file size limits, and mis‑placed details, and reports issues to fix.

### Greenfield: `/lsdd-init-new`

- User (main chat): `/lsdd-init-new`
- LeanSDD: Confirms that this is a new project, checks for the presence of a Constitution in `AGENTS.md`/`CLAUDE.md` (or equivalent), and asks clarifying questions about the product and constraints.
- LeanSDD (main chat): Helps refine the Constitution text (one‑paragraph description plus code best practices), then proposes a minimal set of initial FRs (major user journeys or public APIs), NFR areas, and likely subsystems.
- LeanSDD (in a sub‑task, `LeanSDD` mode): Creates short, skeletal `docs/fr-*.md`, `docs/nfr.md`, `docs/glossary.md`, and `docs/architecture.md`/`docs/sub-*.md` entries that respect LeanSDD limits and leave detailed design to the future Specification‑Driven Change workflow.
- LeanSDD (main chat): Summarizes what was written, suggests staging the new spec files, and reminds you to use `/lsdd-change` for the first real feature change.

### Specification‑Driven Change: `/lsdd-change`

- User (main chat): `/lsdd-change` and describes the desired change.
- LeanSDD: Checks for a clean Git state, then asks targeted clarifying questions about the change and which parts of the system it affects.
- LeanSDD (in a sub‑task, `LeanSDD` mode): Updates the relevant FRs and, if needed, NFRs and Glossary entries, keeping each spec file small, cohesive, and free of UI copy or data‑model details.
- LeanSDD (in another sub‑task, `LeanSDD` mode): Updates Architecture and Subsystem specs to reflect the same change, using `git diff` on the spec files from the previous step as context.
- LeanSDD (main chat): Proposes staging the updated spec files, spawns a critique sub‑task to review them for violations of LeanSDD rules, then summarizes the final spec delta and tells you to hand off to a Coder agent with the staged specs.

### Code‑Driven Change: `/lsdd-reconcile`

- User (main chat): Stages only the code changes, then runs `/lsdd-reconcile`.
- LeanSDD: Verifies that only code (no spec files) is staged and asks which feature or bug the changes are meant to address.
- LeanSDD (in a sub‑task, `ask` mode): Uses a large context to inspect the staged diff and nearby code, summarizing behavior changes and potential impacts on existing FR/NFR/Architecture/Subsystem specs.
- LeanSDD (in a sub‑task, `LeanSDD` mode): Proposes minimal, destructive edits to the affected spec files so that they match the staged code behavior, keeping them concise and within their roles.
- LeanSDD (main chat): Suggests staging the spec changes separately, runs a quick critique sub‑task to spot inconsistencies or bloated specs, and summarizes options (adjust code, adjust specs, or split into multiple changes).

