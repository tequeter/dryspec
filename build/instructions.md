# Instructions common to all AI runtimes

This file defines the DrySpec rules and prompt‑design constraints that apply
to any AI runtime (Codex CLI, RooCode, Claude, etc.). Runtime‑specific
instruction files (for example `build/instructions-codex.md` and
`build/instructions-roocode.md`) SHOULD treat this file as the single source
of truth for shared behavior and only add what is unique to that runtime.

## Shared DrySpec knowledge

`SPECIFICATION.md` is the single source of truth for DrySpec semantics and workflows.
Any runtime-specific system prompt or mode that implements DrySpec MUST encode the
rules from `SPECIFICATION.md` in summarized form (do not copy large chunks verbatim),
in addition to the Specification index table.

Minimum coverage checklist:

- **Constitution + Specification index**
  - Constitution contents and why it’s always loaded.
  - The agent SHALL NOT edit the Constitution unless the user explicitly asks.
  - The Constitution’s Specification index overrides the embedded generic table.

- **Agile specification constraints**
  - DRY, short, dense; explicit per-file size limits.
  - Prefer destructive, coherent edits; forbid incremental spec patches and “proposed changes”.
  - Keep files loosely coupled and highly cohesive (one responsibility per file).
  - Keep detailed internal interfaces and tests beyond acceptance out of scope; minor decisions stay with the coder agent + user.
  - Distinguish internal semantics (errors, conditions) from external presentation (UX vernacular).

- **File roles and boundaries**
  - FRs: user-visible requirements (or public surface for libraries); sparse acceptance scenarios; priorities MAY be MoSCoW.
  - NFRs: cross-cutting qualities/constraints; not implementation details.
  - Glossary: domain/project term definitions.
  - Architecture + Subsystems: major components and internal interfaces between subsystems; no code/pseudo-code; no method signatures.
  - “Other files” out-of-scope boundaries: data models, external contracts, UI wording (do not create/update unless explicitly instructed).

- **Headings & stable links**: Spec items intended to be referenced later MUST be addressable via their heading-derived section identifier: use plain, stable, unique natural-language headings as the sole in-file identifiers, and do not include any literal section-link fragments or inline-ID syntax in the runtime prompt/mode.
- **Cross-repository linking** via canonical URLs.
- **Workflows**
  - Git usage assumptions (clean state when it matters; staging as protection; Git ops user-confirmed).
  - Context management expectations (small, step-scoped contexts; prefer fresh tasks over accumulating unrelated history).

## Specification index rules

All DrySpec-aware runtime prompts and modes SHALL follow these rules for the
Specification index:

- Embed a verbatim copy of the "Specification index" table from `SPECIFICATION.md` as
  a generic default, so it can be used when bootstrapping projects that do not
  yet have a Constitution.
- Make it explicit that when a project-specific Constitution (for example
  `AGENTS.md`, `CLAUDE.md`, or similar) contains its own "Specification index"
  section, that Constitution index is the single source of truth for file
  locations, roles, and size limits.
- In case of any discrepancy, the Constitution's index SHALL override the
  generic one baked into the runtime prompt or mode.

## Shared prompt-design rules

These constraints apply to the static system prompts / modes for all runtimes:

- **Self-contained and repo-agnostic**
  - The runtime prompt or mode MUST be self-contained with respect to DrySpec:
    do not rely on `SPECIFICATION.md` being present at runtime.
  - MUST NOT reference "the DrySpec repo" or similar: it is not accessible and
    SHOULD NOT be mentioned to the user.

- **Use of `SPECIFICATION.md`**
  - Do **not** paste large chunks of `SPECIFICATION.md` verbatim into runtime prompts
    or modes, **except** for the "Specification index" table described above.
  - For all other DrySpec content, compress and paraphrase so the system text
    stays short, dense, and DRY while still giving the agent enough static
    knowledge to operate without this repo.

- **Runtime context**
  - Assume that at runtime the agent will see:
    - The host project's Constitution (via `AGENTS.md`, `CLAUDE.md`, etc.),
      which SHOULD already embed the generic Specification index.
    - The current project's specs and code, but not this DrySpec repo.
    - The user's current message, which drives the concrete task (for example,
      drafting specs, refining them, reconciling specs and code, or critique).

## Challenges

### Ignored authoring instructions

LLMs tend to ignore conciseness, size limits, "SHALL NOT have", and destructive
editing instructions.

Workarounds that ALL runtimes MUST encode in their prompts/modes:

- Emphasize conciseness, size limits, and "SHALL NOT" constraints explicitly.
- Prefer destructive, coherent edits to specs over incremental patch notes.
- Include a critique/review pattern that checks whether newly authored or
  modified specs violated these rules and suggests concrete fixes.

### Ignored "Ask clarifying questions" instructions

LLMs often ignore a vague "ask clarifying questions" sentence.

Workarounds:

- Encode concrete patterns near the start of the runtime prompt or mode:
  - Ask a short, focused list of clarifying questions when the goal,
    constraints, or target specs are ambiguous instead of inventing missing
    context.
  - Treat the user's request as the primary driver: do not invent hidden
    workflows or speculative subtasks.
- In critique/review steps, explicitly look for hidden assumptions and ask the
  user to confirm or correct them.

## Build-time instructions (Codex CLI)

This section applies only when **Codex CLI is used as the build agent inside this DrySpec repository**
(for example, to generate or update `packages/*` based on `build/instructions-*.md`).
It does **not** describe behavior for end-users who load the resulting `packages/Codex/*` prompts into Codex
while working in a client project.

- Read `SPECIFICATION.md` very carefully. Because it exceeds the tool output limit,
  when using `shell_command` you SHOULD read it in two calls of about
  150 lines each.
- Do not explore files other than those explicitly listed in the task
  instructions (typically `SPECIFICATION.md`, this file, and one runtime-specific
  `build/instructions-*.md` file); other files are not relevant to the
  prompt-design tasks.
- This repo currently does not have an `AGENTS.md` or similar Constitution
  file of its own; do not try to find one when working on prompt-design tasks.
