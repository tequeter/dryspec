# Instructions common to all AI runtimes

This file defines the LeanSDD rules and prompt‑design constraints that apply
to any AI runtime (Codex CLI, RooCode, Claude, etc.). Runtime‑specific
instruction files (for example `build/instructions-codex.md` and
`build/instructions-roocode.md`) SHOULD treat this file as the single source
of truth for shared behavior and only add what is unique to that runtime.

## Shared LeanSDD knowledge

Any runtime-specific system prompt or mode that implements LeanSDD MUST embed
these concepts from `README.md` (summarized in your own words, not copied
verbatim), in addition to the Specification index table:

- **Constitution**
  - Explain what the Constitution is (location, contents, always-loaded
    nature) and how it relates to the rest of the specs.
  - State clearly that the agent SHALL NOT edit the Constitution unless the
    user explicitly asks for that change.

- **Agile specifications**
  - Emphasize DRY, concise specs with explicit size limits.
  - Keep files loosely coupled and highly cohesive: one clear responsibility
    per spec file.
  - Separate internal semantics from UI wording; UI copy belongs in dedicated
    wording files, not in FRs/NFRs.
  - Prefer sparse, high-signal acceptance scenarios instead of exhaustive
    test catalogs.
  - Forbid incremental spec patches and "proposed changes" inside specs;
    edits SHOULD be destructive and coherent.
  - Clarify that detailed internal interfaces and tests beyond acceptance are
    out of scope for specs, and that minor design decisions are left to the
    coder agent and its human operator.

- **File roles and semantics**
  - Functional Requirements (FRs): major user-visible behavior for apps, or
    public surface for libraries. Acceptance scenarios MAY be Gherkin-like,
    and each scenario SHOULD have a stable identifier slug (for example
    `happy-path`), but this identifier SHALL be implicit: choose a heading
    like `### Happy path` and rely on automatic `#happy-path` anchors. Do not
    repeat the slug in the heading text (no `{#happy-path}`, no
    <code>(`happy-path`)</code>, etc.). FRs MAY record priority using MoSCoW.
  - Non-Functional Requirements (NFRs): cross-cutting quality and constraint
    requirements, not implementation details.
  - Glossary: definitions of important domain and project terms.
  - Architecture and Subsystems: describe major components and their internal
    interfaces between subsystems. If there is no global `docs/architecture.md`,
    capture relevant architectural and technical choices in subsystem specs.
    Subsystem specs MAY name major classes/functions in their internal
    interfaces but SHALL NOT go down to method signatures or contain code or
    pseudo-code.

- **Other files and boundaries**
  - Respect the boundaries described in "Other files" (data models, external
    contracts, UI wording).
  - The agent SHALL NOT create or update such files unless explicitly
    instructed, and SHALL avoid stuffing these details into FR/NFR/
    Architecture/Subsystem specs.

- **Cross-repository linking and workflows**
  - Support cross-repo linking via canonical URLs when multi-repo systems
    reference documentation in other repos.
  - Assume the Git usage described in the "Workflows" section of `README.md`:
    clean working tree when it matters, staging (`git add`) as protection for
    spec changes, and user-confirmed Git operations.
  - Encourage minimal, step-scoped contexts instead of accumulating large,
    unfocused histories.

## Specification index rules

All LeanSDD-aware runtime prompts and modes SHALL follow these rules for the
Specification index:

- Embed a verbatim copy of the "Specification index" table from `README.md` as
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
  - The runtime prompt or mode MUST be self-contained with respect to LeanSDD:
    do not rely on `README.md` being present at runtime.
  - MUST NOT reference "the LeanSDD repo" or similar: it is not accessible and
    SHOULD NOT be mentioned to the user.

- **Use of `README.md`**
  - Do **not** paste large chunks of `README.md` verbatim into runtime prompts
    or modes, **except** for the "Specification index" table described above.
  - For all other LeanSDD content, compress and paraphrase so the system text
    stays short, dense, and DRY while still giving the agent enough static
    knowledge to operate without this repo.

- **Runtime context**
  - Assume that at runtime the agent will see:
    - The host project's Constitution (via `AGENTS.md`, `CLAUDE.md`, etc.),
      which SHOULD already embed the generic Specification index.
    - The current project's specs and code, but not this LeanSDD repo.
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

## Build-harness instructions (Codex CLI)

When this repo is used from Codex CLI (for example, to generate prompts or
mode files based on `build/instructions-*.md`):

- Read `README.md` very carefully. Because it exceeds the tool output limit,
  when using `shell_command` you SHOULD read it in two calls of about
  150 lines each.
- Do not explore files other than those explicitly listed in the task
  instructions (typically `README.md`, this file, and one runtime-specific
  `build/instructions-*.md` file); other files are not relevant to the
  prompt-design tasks.
- This repo currently does not have an `AGENTS.md` or similar Constitution
  file of its own; do not try to find one when working on prompt-design tasks.
