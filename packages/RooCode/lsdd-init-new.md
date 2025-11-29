---
description: Initialize LeanSDD specifications for a new project (Greenfield).
---

You are orchestrating the LeanSDD Greenfield workflow in RooCode.

High-level goals:

1. Help the user write or refine the Constitution.
2. Derive an initial, minimal set of LeanSDD specs (FRs, NFRs, Glossary, Architecture, Subsystems).
3. Set up the repo so that future work uses the Specification-Driven Change workflow.

Follow these rules and steps:

0. Before you start, use the `update_todo_list` tool to record the main steps of this workflow as a shared checklist, and update it as you progress (adding, reordering, or ticking off items).

1. **Select modes and context**
   - Use `switch_mode` to ensure you are in the `LeanSDD` mode for specification work.
   - Use `new_task` when you need a focused context to draft or critique a subset of spec files; describe the task and include all necessary information in the sub-task instructions.

2. **Confirm Greenfield status and check Git**
   - Ask the user to confirm that this is a new project or that existing code/specs can be treated as disposable.
   - Run an appropriate Git status command to verify the current state; if it is not clean, summarize the situation and ask how to proceed before creating or editing spec files.

3. **Constitution first**
   - Help the user craft or refine the Constitution (kept in `AGENTS.md`, `CLAUDE.md`, or equivalent):
     - A one-paragraph description of what the software does and why.
     - Project-wide code best practices and constraints.
     - A project-specific Specification index based on the generic LeanSDD table (for example by copying and tailoring the index from the LeanSDD README, possibly changing the root directory from `docs/specs/` if they prefer a different layout).
   - Keep the Constitution concise and high-signal; do not replicate future FR/NFR details here.

4. **Elicit requirements and architecture outline**
   - Ask a short series of clarifying questions about:
     - Target users and primary user journeys or public APIs.
     - Key quality attributes (performance, reliability, security, etc.).
     - Expected high-level architecture (e.g., services, tiers, major components).
   - Summarize the answers and propose:
     - A small set of FR files (major user journeys or API areas).
     - Initial NFR areas for the NFR spec file (for example `docs/specs/nfr.md` in the generic layout).
     - Candidate subsystems for the Subsystem spec files (for example `docs/specs/sub-*.md` in the generic layout) and whether a global Architecture spec file (for example `docs/specs/architecture.md`) is warranted.

5. **Draft minimal spec skeletons**
   - In `LeanSDD` mode (possibly using a `new_task`), create or refine:
     - FR spec files (for example `docs/specs/fr-*.md` in the generic layout) with:
       - A short description from the user’s point of view.
       - A few high-signal acceptance scenarios, each with a stable identifier, leaving detailed behavior for later refinement.
     - The NFR file (for example `docs/specs/nfr.md` in the generic layout) with headings for the main NFR areas and concise bullets.
     - The Glossary file (for example `docs/specs/glossary.md` in the generic layout) with only the most important domain terms, each defined in a few sentences.
     - The Architecture and Subsystem spec files (for example `docs/specs/architecture.md` and `docs/specs/sub-*.md` in the generic layout) describing the intended high-level structure and key subsystems, naming important internal interfaces without going into signatures or code.
   - Explicitly avoid writing detailed data models, external API contracts, or UI phrase-books; mention them only at a high level when needed.
   - Keep all files well within LeanSDD size limits; favor placeholders and TODO markers over bloated prose.

6. **Protect and critique**
   - Propose staging the new spec files (for example with `git add` on the paths from the project’s Specification index, such as `docs/specs/...` in the generic layout) and clearly list what will be staged; ask for user confirmation before executing Git commands.
   - Launch a `new_task` in `LeanSDD` mode dedicated to critiquing the staged specs. In that sub-task:
     - Check that each file respects its role and boundaries.
     - Check for unnecessary verbosity, repeated content, or hidden assumptions.
     - Produce a concise list of potential improvements and questions for the user.
   - Apply only the improvements that the user approves.

7. **Hand-off and next steps**
   - Summarize the initial LeanSDD spec structure (FRs, NFRs, Glossary, Architecture, Subsystems) and how it reflects the Constitution.
   - Recommend using `/lsdd-change` for future features and non-trivial changes and `/lsdd-reconcile` when code changes precede spec updates.
