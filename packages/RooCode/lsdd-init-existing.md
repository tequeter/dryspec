---
description: Initialize LeanSDD specifications for an existing codebase (Brownfield).
---

You are orchestrating the LeanSDD Brownfield workflow in RooCode.

High-level goals:

1. Discover the architecture and subsystems of an existing codebase.
2. Bootstrap concise LeanSDD specs (Architecture, Subsystems, FRs, NFRs, Glossary) from what you observe.
3. Leave the repo in a state where specs are staged and ready for critique and later implementation work.

Follow these rules and steps:

1. **Select modes and context**
   - Use `switch_mode` to ensure you are in the `LeanSDD` mode for specification work.
   - Use `switch_mode` to move into the built-in `ask` mode when you need to read and summarize sizable chunks of code or diffs (large-context code exploration).
   - Use `new_task` whenever you need a fresh, focused context (what the UI calls "in a sub-task"), especially for:
     - Deep dives into individual subsystems.
     - Authoring or critiquing a specific subset of spec files.

2. **Check Git and clarify the scope**
   - Run an appropriate Git command (for example, `git status --porcelain`) to check whether the working tree is clean.
   - If there are uncommitted or unstaged changes, clearly summarize them and ask the user how to proceed; do not assume you may overwrite them.
   - Check whether a Constitution file (for example in `AGENTS.md`, `CLAUDE.md`, or an equivalent always-loaded file) is present; if it is missing or clearly incomplete, pause the workflow and ask the user to create or refine it before continuing.
   - Ask a short, focused list of clarifying questions about the application/library, main technologies, and what parts of the repo are most important to cover first.

3. **Propose subsystems and plan the exploration**
   - Using the repo structure (for example, 1–2 levels under `src` or similar), propose a candidate list of subsystems that would map well to `docs/sub-*.md`.
   - Keep the proposal concise and explain how each subsystem boundary relates to code directories or modules.
   - Agree with the user on which subsystems to explore first.

4. **Subsystem deep dives (in sub-tasks)**
   - For each chosen subsystem, start a `new_task` in the `ask` mode with instructions that:
     - Point at the relevant directories/files.
     - Ask for a short, dense summary of responsibilities, important entry points, external behaviors, and notable constraints.
     - Ask the sub-task to flag candidate FRs, NFR areas, and Glossary terms without fully specifying FRs.
   - In each sub-task, keep outputs compact and focused; avoid restating code and avoid inventing UI copy or data models.
   - Bring back only the essential findings to the main LeanSDD task.

5. **Bootstrap specs from findings**
   - In `LeanSDD` mode, use the collected subsystem findings to:
     - Draft or refine `docs/architecture.md` with system-wide structure and key technical choices.
     - Draft or refine `docs/sub-*.md` for each subsystem, describing responsibilities and high-level internal interfaces (classes/modules/functions), but not method signatures.
     - Identify major user-visible behaviors or public API areas and create initial `docs/fr-*.md` files that:
       - Are written from the user’s perspective.
       - List a small number of high-signal acceptance scenarios, each with a stable anchor identifier.
     - Populate `docs/nfr.md` with clearly headed NFRs and `docs/glossary.md` with concise term definitions.
   - Enforce concision and LeanSDD size limits; refactor existing text if needed instead of appending "notes" sections.

6. **Protect work with Git and critique**
   - Suggest staging the spec files you created or edited (for example with `git add docs/...`) and clearly explain which paths you are about to stage.
   - Remind the user that they SHOULD confirm any Git operation before you run it.
   - Start a `new_task` in `LeanSDD` mode to critique the staged specs. In that sub-task:
     - Check that each concern (FR, NFR, Glossary, Architecture, Subsystem) lives in the right file type.
     - Check that files are short, DRY, and free from UI phrase-books, data-model schemas, or external contract details.
     - Identify hidden assumptions or missing clarifications and list concrete questions or follow-ups.
   - Report critique findings back in the main task and, if the user agrees, apply targeted corrections.

7. **Hand-off**
   - Summarize the newly created or updated specs and how they map to the discovered subsystems.
   - Explicitly suggest next steps, typically:
     - Use `/lsdd-change` for Specification-Driven Changes.
     - Use `/lsdd-reconcile` for reconciling future code changes with these specs.
