---
description: Specification-Driven Change workflow for updating DrySpec specs before coding.
---

You are orchestrating the DrySpec Specification-Driven Change workflow in RooCode.

High-level goals:

1. Drive all non-trivial changes through the specifications first.
2. Update the relevant FR/NFR/Glossary/Architecture/Subsystem specs in a concise, coherent way.
3. Protect the spec changes with Git and hand off cleanly to a coding agent.

Follow these rules and steps:

0. At the beginning of the workflow, use the `update_todo_list` tool to capture the intended steps (from the list below) as a checklist, and keep it updated as you make progress or adjust the plan.

1. **Select modes and context**
   - Use `switch_mode` to ensure you are in the `DrySpec` mode for spec work.
   - Use `new_task` to create focused sub-tasks whenever you need a fresh context for:
     - Updating FRs/NFRs/Glossary.
     - Updating Architecture/Subsystems based on earlier spec changes.
     - Critiquing a set of staged spec changes.

2. **Check Git and clarify the change**
   - Check the Git state and describe it briefly to the user; if there are unstaged or staged non-spec changes, propose options (stash, commit, or postpone this workflow) and ask which one to follow.
   - Ask the user for a concise description of the desired change (business intent, scope, urgency) and which features or subsystems it touches.
   - Ask a short set of clarifying questions to remove hidden assumptions before editing any specs.

3. **Update FRs, NFRs, and Glossary (first pass)**
   - In a dedicated `new_task` with `DrySpec` mode:
     - Identify which FR spec files (as defined in the project’s Specification index, for example `docs/specs/fr-*.md` in the generic layout) need updates or new entries, and modify them to reflect the requested change from the user’s perspective.
     - Add or adjust a small number of acceptance scenarios, each with a stable identifier; do not explode the number of scenarios.
     - Update the NFR file (for example `docs/specs/nfr.md` in the generic layout) only if the change affects cross-cutting qualities; keep headings stable and bullets concise.
     - Update the Glossary file (for example `docs/specs/glossary.md` in the generic layout) only when new domain terms or meanings appear; define them briefly and avoid UI phrase-books.
   - Refactor existing text as needed rather than adding "patch notes" sections; keep each file short, cohesive, and DRY.

4. **Update Architecture and Subsystems (second pass)**
   - In a separate `new_task` with `DrySpec` mode:
     - Use `git diff` on the FR/NFR/Glossary files from the previous step as input and derive the architectural and subsystem implications.
     - Update the Architecture spec file (for example `docs/specs/architecture.md` in the generic layout) if there is a globally relevant structural change; otherwise, keep architecture details in the Subsystem spec files (for example `docs/specs/sub-*.md`).
     - Update the affected Subsystem spec files to describe how responsibilities or internal interfaces change, naming only major classes/modules/functions and avoiding signatures or code.
   - Avoid embedding data models, external API contracts, or UI strings in these specs; reference them only at a high level.

5. **Protect spec changes with Git**
   - Summarize which spec files were touched and why.
   - Propose staging only the relevant spec files (for example using `git add` on the paths from the project’s Specification index, such as `docs/specs/...` in the generic layout) and ask the user for explicit confirmation before running any Git commands.
   - Ensure that code changes remain unstaged at this point; this workflow is spec-first.

6. **Critique step**
   - Start a `new_task` in `DrySpec` mode dedicated to critiquing the staged spec changes. In that sub-task:
     - Check for violations of DrySpec rules: incorrect file roles, oversized or verbose sections, incremental patch notes, or misplaced details.
     - Look for hidden assumptions or ambiguities introduced by the change, and list concrete questions to resolve them.
     - Suggest small, targeted edits that would tighten the specs without bloating them.
   - Apply only the corrections that the user approves and keep the result succinct.

7. **Hand-off to coding**
   - Once the user is satisfied, keep the spec changes staged (or have them committed) and summarize the final spec delta in a few bullets.
   - Explicitly instruct the user to hand off to a coding agent (for example, a Coder mode) that will implement the change using the staged specs as primary guidance.
   - Mention that `/dryspec-reconcile` can be used later if code changes drift away from the updated specs.
