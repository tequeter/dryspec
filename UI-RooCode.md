# LeanSDD in RooCode

This guide assumes you have read `README.md` and already configured your Constitution and Specification Index.

## Modes

- `Spec Author (LeanSDD)` (`lsdd-author`)
  - Writes and refactors the LeanSDD specification (FRs, NFRs, architecture, glossary).
  - Focuses on destructive editing: rewrite or trim existing text instead of adding appendices or "notes".
  - Asks concrete clarifying questions before making non-trivial changes.
  - Keeps files within the limits defined in the Specification Index.
- `Spec Critic (LeanSDD)` (`lsdd-critic`)
  - Reviews the specification for concision, hidden assumptions, and compliance with LeanSDD rules.
  - Highlights violations of size limits, "SHALL NOT" requirements, and missing clarifying questions.
  - Suggests targeted edits instead of rewriting everything.
- Built-in `ask` mode
  - Used for focused code or repo deep-dives.
  - Typically runs in a sub-task, then reports back short, high-signal findings to Spec Author or Spec Critic.

## Slash-commands

- `/lsdd-brownfield`
  - Guides the Brownfield workflow for an existing codebase.
  - Helps you discover subsystems, architecture, candidate FRs/NFRs, and glossary entries from the code.
- `/lsdd-greenfield`
  - Guides the initial setup for a new project after you have a Constitution.
  - Helps you sketch the first FRs, NFRs, architecture, and glossary at LeanSDD granularity.
- `/lsdd-change`
  - Guides the Specification-Driven Change workflow.
  - Helps you update FRs/NFRs, architecture, and subsystems before coding.
- `/lsdd-code-change`
  - Guides the Code-Driven Change workflow starting from staged code changes.
  - Helps you find inconsistencies between the code and the existing specification.

## Sample sessions

### Brownfield

1. Start in `Spec Author (LeanSDD)`.
2. User: `/lsdd-brownfield`
3. Spec Author:
   - Confirms that the Constitution is ready.
   - Proposes a list of subsystems and a short exploration plan.
4. User: "Open an `ask` sub-task on the backend service and summarize its behavior."
5. Spec Author (in a sub-task, ask mode):
   - Deep-dives into the selected folders.
   - Returns a short summary plus candidate FRs/NFRs and glossary terms.
6. Spec Author (main task):
   - Uses the findings to initialize or refactor `docs/architecture.md`, `docs/fr-*.md`, `docs/nfr.md`, and `docs/glossary.md`, staying within the Specification Index limits.
7. User: "Switch to Spec Critic (LeanSDD) in a sub-task and review the new spec."

### Greenfield

1. Start in `Spec Author (LeanSDD)`.
2. User: `/lsdd-greenfield`
3. Spec Author:
   - Asks clarifying questions about the product, constraints, and risks.
   - Ensures the Constitution is complete and consistent.
   - Proposes an initial list of FR documents, NFR areas, and major subsystems.
4. Spec Author (in a sub-task):
   - Drafts concise initial versions of `docs/fr-*.md`, `docs/nfr.md`, `docs/architecture.md`, and `docs/glossary.md`, obeying the Specification Index.
5. User: "Switch to Spec Critic (LeanSDD) in a sub-task and critique the initial specification."

### Specification-Driven Change

1. Start in `Spec Author (LeanSDD)`.
2. User: `/lsdd-change`
3. Spec Author:
   - Asks clarifying questions about the intended change and its impact.
   - Identifies which FRs/NFRs, subsystems, and architecture sections are affected.
4. Spec Author (in a sub-task):
   - Updates the relevant FRs and NFRs first, using destructive editing and keeping them within limits.
5. Spec Author (in another sub-task):
   - Updates `docs/architecture.md` and any affected subsystem specs, using `git diff` as context when useful.
6. User: stage the specification changes in Git.
7. User: "Switch to Spec Critic (LeanSDD) in a sub-task and run a focused review of the staged spec changes."

### Code-Driven Change

1. Implement and stage your code changes.
2. Start in `Spec Author (LeanSDD)` or `Spec Critic (LeanSDD)`.
3. User: `/lsdd-code-change`
4. The agent:
   - Looks at the staged diff and the existing specification.
   - Identifies where the code and spec disagree or where the spec is silent.
   - Asks clarifying questions instead of guessing intent.
5. The agent proposes options:
   - Adjust the code to match the spec.
   - Or adjust the spec (via `/lsdd-change` in Spec Author, typically in a sub-task).
6. After the spec is updated and staged, run `lsdd-critic` again in a sub-task to check for hidden assumptions or missed constraints.

