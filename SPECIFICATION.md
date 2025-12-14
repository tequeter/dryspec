# LeanSDD specification

## Agile specifications

Good specifications:

- Must be DRY, concise, short, and dense: AI tends to be overly verbose and that's not required for a decent coder model. Huge specifications eat up the context, are harder to maintain, and produce little value.
  - For example, internal interfaces and tests beyond acceptance are out of scope.
  - Minor decisions are left to the coder agent and its human operator.
- Must not leave room for architectural improvisation, without being over-specified (see previous point).
- Must be made of loosely coupled files loaded into the LLM's context as needed. Each file must have high cohesion.
- Must distinguish internal semantics (error types, conditions) from external presentation such as UX vernacular.
- Must include sparse, high-signal, acceptance test scenarios to complement the concise prose.
- Must have a size limit on each file to force concision, test sparseness, and module cohesion.
- Must not produce incremental "specification patches" (neither as a "notes" chapter nor as extra files). They will become outdated quickly and bloat the LLM context. Instead, refactor destructively the existing specifications as required, without losing existing detail. Their history is forever available through Git.
- Must not contain proposed changes: that's what Git feature branches and conflict resolution tools are for.

The workflows and the specifications must be optimized for the incremental changes that come with agile practices, rather than clinging to the obsolete waterfall model.


## Specification structure

### Constitution

The Constitution is always loaded into the context, for all agents, so it is part of `AGENTS.md`, `CLAUDE.md`, or a similar file. It contains:

1. A short description of the program in one paragraph. What the software does (features), why, what problem it solves.
2. Code best practices.
3. A specification index for this project. Start with the generic template provided below, and tune the limits if needed (some paths are embedded in system prompts).

The user provides the Constitution (the description is quick to write, and the best practices are supposedly already identified before the project).

The agent SHALL NOT edit the Constitution without an explicit request from the user.

#### Specification index

<!-- markdownlint-disable MD033 -->

> The formal specifications are organized as follows:
>
> | File                         | Notes                                           | Upper limits per file (target: half) |
> | ----------------------       | ----------------------------------------------- | ---------------------- |
> | `docs/specs/fr-*.md`         | For user-visible features or library interface. | 1200 words <br> 3 short paragraphs context <br> 10 bullets behavior (each 2 sentences max) <br> 7 scenarios (each 8 lines max). |
> | `docs/specs/nfr.md`          | For non-functional requirements.                | 1300 words, 10 NFR areas, 40 bullets total. |
> | `docs/specs/glossary.md`     | Optional.                                       | 1600 words, 40 terms (3 sentences each). |
> | `docs/specs/architecture.md` | Optional.                                       | 1600 words |
> | `docs/specs/sub-*.md`        | For the major internal software components.     | 1300 words |

<!-- markdownlint-restore -->

Projects MAY adapt this table to their own directory layout, file naming, and per-file limits. The project-specific Specification index in the Constitution is the single source of truth for the actual paths and limits.

### Functional Requirements (FR)

The software SHALL have functional requirements documented (generic layout: `docs/specs/fr-*.md`). This is the What and Why.

- For an application, do specify the major, user-visible requirements.
- For a library, do specify the interface.
- Do specify end-to-end journeys (e.g. sign-up -> email verification -> onboarding) as separate FRs.
- Do not specify anything else as functional requirements.

The FR specification file:

- SHALL have: a description from the point of view of the user.
- SHOULD have: acceptance scenarios, again from the point of view of the user.
  - They MAY use the Gherkin structure.
  - They SHALL have an identifier to reference in associated executable tests (e.g. `fr-login.md#happy-path`).
- All sections combined, it SHALL describe the expected behavior unambiguously, including the error conditions.
- MAY have a priority (MoSCoW method) expressed.
- SHALL have a consistent and semantically meaningful name (for example, avoid generic names like `fr-core.md` in the default layout).
- Unambiguously identifies the FR. There SHALL NOT be a second canonical identifier listed inside the file. Scenario-level anchors `#happy-path` are fine.

### Non-functional Requirements (NFR)

The software SHALL have non-functional requirements documented (generic layout: `docs/specs/nfr.md`). This is a single file for ease of loading into a coding agent's context.

To allow deep hyperlinking, individual NFRs must have simple, immutable headings.

Common NFR categories include:

- Performance
- Reliability, availability, maintainability and safety
- Scalability
- Testability

See also: [Non-functional requirement](https://en.wikipedia.org/wiki/Non-functional_requirement) on Wikipedia.

### Glossary

The software SHOULD have a Glossary file (for example `docs/specs/glossary.md` in the generic layout) concisely defining the non-obvious terms and domain concepts used throughout the specification, code, and UI.

The Glossary SHALL NOT be used as a UI phrase-book.

### Architecture

The software MAY have a general architecture and technical choices document (generic layout:  `docs/specs/architecture.md`). This is the How.

If not specified, that information must be provided in the subsystems.

### Subsystems

We call subsystems the major internal software components, delimited by stable code boundaries (modules, packages, crates, ...).

Each subsystem must be specified in a Subsystem spec file (generic layout: `docs/specs/sub-*.md`).

The file SHALL at least contain a description of what the subsystem does.

Additionally:

1. It MAY describe the architecture and technical choices scoped to that subsystem.
2. It SHOULD name the major classes, functions etc. part of its internal interface (from one subsystem to another).

It SHALL NOT further describe the internal interface (methods, function arguments etc.). That information can be obtained in more effective ways (codebase index) and is subject to change in every refactor.

It SHALL NOT include subsystem code, pseudo-code, or anything of that sort.

### Other files

The user MAY create other specification files.

Most notably, these topics are currently out of LeanSDD's scope. The agent SHALL NOT attempt to create or update them without explicit instruction by the user.

- The data models (schemas, invariants, privacy constraints).
- The external contracts (third-party APIs, message formats).
- The UI wording.


## Cross-repository support

Software that spans multiple repositories MAY cross-link to arbitrary, read-only documentation in other repos through canonical URLs.


## Workflows

About Git usage:

- LeanSDD leverages Git to track ongoing changes and protect them from editing/AI mishaps.
- Protection is typically done by staging (`git add`), but the user may perform an intermediate commit instead.
- All workflows assume an initial **clean Git state** unless noted. The agent SHOULD check for it.
- The workflows feature instructions to alter the Git state (`git add`, `git commit`). The agent MAY execute them, but the user SHOULD carefully OK each such attempt.

About context management:

- The user or agent SHOULD use minimal, focused contexts scoped to the current step when possible. This produces better results and saves tokens.

"Loading LeanSDD" (into the LLM context) may mean changing mode, entering a slash-command etc., [depending on your AI runtime](README.md#supported-runtimes).

### Brownfield

For an existing project, from the perspective of the user,

- Complete your Constitution.
- Load LeanSDD and let the agent explore the codebase.
  - The agent SHALL check the presence of all 3 elements of the Constitution in `AGENTS.md` or equivalent.
  - The agent SHOULD suggest a list of subsystems by listing 1-2 levels of directories, appropriate to the current language and project (such as: list `src`).
  - You SHOULD use a dedicated context (sub-agent, sub-task) to deep-dive into each Subsystem.
  - The sub-agent SHALL document the Subsystem, asking clarifying questions as necessary.
  - The sub-agent SHALL return Architecture, FRs, NFRs, and Glossary concise observations to the calling agent.
  - In particular, FRs are hard to reverse-engineer. The sub-agent SHALL stop at identifying possible FRs and SHALL NOT attempt describing them fully.
- Have the calling agent document the observed Architecture, NFRs, and Glossary, again asking questions as necessary. Also init the FRs files with what was collected.
- Rework the collected information:
  - Rework the Glossary, each FR individually, the Architecture, and the NFRs.
  - If you deem agent assistance useful, use a clean context every time, reloading (just) the necessary context using `git diff`.
  - The FRs identified through codebase exploration are just a starting point and will need heavy editing.
  - The agent SHOULD suggest this step as a reminder of the process, but not perform any action on its own. The user will open supplemental chat sessions on their own if needed.
- Stage your changes.
- Clear and load LeanSDD again, then ask the agent to critique your specification, looking for inconsistencies. Fix as needed.

### Greenfield

Creating a new project (greenfield deployment) just means filling in the Constitution as described above.

Then use the Specification-Driven Change workflow below to create the rest of the specification.


### Specification-Driven Change

This is the main workflow, and the user SHOULD use it for all non-trivial changes.

The agent SHALL interact with and guide the user through creating or updating the specification as per the above framework.

If useful for the task at hand, the agent SHOULD gather context using the provided tools and ask as many clarifying questions as necessary to the user.

From the perspective of the user:

- If FRs and/or NFRs need to be updated, start with an empty context, load LeanSDD, and update the Requirements and possibly the Glossary with the help of the agent.
- Clear the context again, load LeanSDD, and update the Architecture and/or the Subsystems with the help of the agent. Tell the agent to look at `git diff` if you changed anything in the previous step.
- Once satisfied with the spec update, stage it for protection.
- You SHOULD now clear the context and have LeanSDD critique your changes. Stage again.
- Implement the change with a Coder agent, pointing it at the staged specification changes.


### Code-Driven Change

This workflow is useful when either the user skipped the specification part, or for a bugfix that didn't seem to warrant touching the specifications.

Either way,

- Start with your code changes (and nothing else) staged in Git.
- Ask LeanSDD to look for inconsistencies between your staged code changes and the existing specifications.
- Fix as you see fit, possibly using a new context depending on the situation. If using an agent, it SHALL ask the user when faced with conflicts.
