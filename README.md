# LeanSDD

## Agile specifications

The specifications are a golden guardrail for the AI dev agent that would otherwise get lost during the implementation phase.

Good specifications:

- Must be DRY, concise, short, and dense: AI tends to be overly verbose and that's not required for a decent coder model. Huge specifications eat up the context, are harder to maintain, and produce little value.
- Must not leave room for architectural improvisation. Yet must not be over-specified (see previous point) - good coding models will handle minor decisions properly without having everything written down prior.
- Must be made of loosely coupled files loaded into the LLM's context as needed. Each file must have high cohesion.
- Must distinguish internal semantics (error types, conditions) from external presentation such as UX vernacular.
- Must include sparse, high-signal, acceptance test scenarios to complement the concise prose.
- Must have a size limit on each file to force concision, test sparseness, and module cohesion.
- Must not produce incremental "specification patches" (neither as a "notes" chapter nor as extra files). They will become outdated quickly and bloat the LLM context. Instead, refactor destructively the existing specifications as required, without losing existing detail. Their history is forever available through Git.

The workflows and the specifications must be optimized for the incremental changes that come with agile practices, rather than clinging to the obsolete waterfall model. See also [Existing SDD Frameworks](FAQ.md#existing-sdd-frameworks).


## Specification layers

You will have these layers:

1. Formal specifications documenting functional Requirements.
2. Formal specifications documenting technical aspects.
3. Implicit specifications such as unit tests, internal interfaces etc.
4. Transient specifications with the agent for the duration of the coding session.

LeanSDD only cares about the first two.


## Specification structure

### Constitution

The Constitution is always loaded into the context, for all agents, so it is part of `AGENTS.md`, `CLAUDE.md`, or a similar file. It contains:

1. A short description of the program in one paragraph. What the software does (features), why, what problem it solves.
2. Code best practices.
3. A generic specification index provided below.

The user provides the Constitution (the description is quick to write, and the best practices are supposedly already identified before the project).

The agent SHALL NOT edit the Constitution without an explicit request from the user.

#### Specification index

> You may read the software specifications here:
> - `docs/fr-*.md` for user-visible features or library interface.
> - `docs/nfr.md` for non-functional requirements.
> - `docs/glossary.md` (optional).
> - `docs/architecture.md` (optional).
> - `docs/sub-*.md` for the major internal software components.

### Functional Requirements

The software SHALL have functional requirements documented as `docs/fr-*.md`. This is the What and Why.

- For an application, do specify the major, user-visible requirements.
- For a library, do specify the interface.
- Do not specify anything else as functional requirements.

Requirements typically have:

1. A description from the point of view of the user.
2. Acceptance scenarios, again from the point of view of the user. MAY use the Gherkin structure. SHALL have an identifier to reference in associated executable tests.

Both combined SHALL describe the expected behavior unambiguously, including the error conditions.

The agent SHALL suggest consistent and semantically meaningful names for the files (no `docs/fr-core.md`).

The file name unambiguously identifies the FR. There SHALL NOT be a different identifier listed inside the file.

#### Non-functional Requirements

The software SHALL have non-functional requirements documented as `docs/nfr.md`. This is a single file for ease of loading into a coding agent's context.

To allow deep hyperlinking, individual NFRs must have simple, immutable headings.

Common NFR categories include:

- Performance
- Reliability, availability, maintainability and safety
- Scalability
- Testability

See also: [Non-functional requirement](https://en.wikipedia.org/wiki/Non-functional_requirement) on Wikipedia.

### Glossary

The software SHOULD have a `docs/glossary.md` concisely defining the non-obvious terms and domain concepts used throughout the specification, code, and UI.

The Glossary SHALL NOT be used as a UI phrase-book.

### Architecture

The software MAY have a general architecture and technical choices document as `docs/architecture.md`. This is the How.

If not specified, that information must be provided in the subsystems.

### Subsystems

We call subsystems the major internal software components (modules, packages, crates, ...).

Each subsystem must be specified as `docs/sub-*.md`.

The file SHALL at least contain a description of what the subsystem does.

Additionally:
1. It MAY describe the architecture and technical choices.
2. It SHOULD name the major classes, functions etc. part of its internal interface (from one subsystem to another).

It SHALL NOT further describe the internal interface (methods, function arguments etc.). That information can be obtained in more effective ways (codebase index) and is subject to change in every refactor.

It SHALL NOT include subsystem code, pseudo-code, or anything of that sort.

### Other files

The user MAY create other files under `docs/`.

## Cross-repository support

Software that spans multiple repositories MAY cross-link to other repos through canonical URLs.


## Workflows

About Git usage:
- LeanSDD leverages Git to track ongoing changes and protect them from editing/AI mishaps.
- Protection is typically done by staging (`git add`), but the user may perform an intermediate commit instead.
- All workflows assume an initial **clean Git state** unless noted. The agent SHOULD check for it.
- The workflows feature instructions to alter the Git state (`git stage`, `git commit`). The agent MAY execute them, but the user SHOULD carefully OK each such attempt.

About context management:
- The workflows use minimal, focused contexts scoped to the current step when possible. This produces better results and saves tokens.

"Loading LeanSDD" (into the LLM context) may mean changing mode, entering a slash-command etc., [depending on your AI runtime](#ui).

### Brownfield (`/lsdd-brownfield`)

For an existing project, from the perspective of the user,
- Complete your Constitution.
- Load LeanSDD and let the agent explore the codebase.
    - You SHOULD use a dedicated context (sub-agent, sub-task) to deep-dive into each Subsystem.
    - The sub-agent SHALL document the Subsystem, asking clarifying questions as necessary.
    - The sub-agent SHALL return Architecture, Requirements, and Glossary concise observations to the calling agent.
    - In particular, Requirements are hard to reverse-engineer. The sub-agent SHALL stop at identifying possible Requirements and SHALL NOT attempt describing them fully.
- Have the calling agent document the observed Architecture and Glossary, again asking questions as necessary. Also init the Requirements files with what was collected.
- Using a clean context again, document the Requirements. Reload (just) the necessary context using `git diff`. The agent MAY suggest iterating on the init'ed Requirement files.
    - If your AI runtime permits it, use a dedicated context to flesh out each Requirement.
    - The Requirements identified through codebase exploration are just a starting point and will need heavy editing by the user.
- Stage your changes.
- Clear and load LeanSDD again, then ask the agent to critique your specification, looking for inconsistencies. Fix as needed.

### Greenfield (`/lsdd-greenfield`)

Creating a new project (greenfield deployment) just means filling in the Constitution as described above.

Then use the Specification-Driven Change workflow below to create the rest of the specification.


### Specification-Driven Change (`/lsdd-change`)

This is the main workflow, and the user SHOULD use it for all non-trivial changes.

The agent SHALL interact with and guide the user through creating or updating the specification as per the above framework.

If useful for the task at hand, the agent SHOULD gather context using the provided tools and ask as many clarifying questions as necessary to the user.

From the perspective of the user;
- If requirements need to be updated, start with an empty context, load LeanSDD, and update the Requirements and possibly the Glossary with the help of the agent.
- Clear the context again, load LeanSDD, and update the Architecture and/or the Subsystems with the help of the agent. Tell the agent to look at `git diff` if you changed anything in the previous step.
- Once satisfied with the spec update, stage it for protection.
- You SHOULD now clear the context and have LeanSDD critique your changes. Stage again.
- Implement the change with a Coder agent, pointing it at the staged specification changes.


### Code-Driven Change (`/lsdd-code-change`)

This workflow is useful when either the user skipped the specification part, or for a bugfix that didn't seem to warrant touching the specifications.

Either way,
- Start with your code changes (and nothing else) staged in Git.
- Ask LeanSDD Spec Critic to look for inconsistencies between your staged code changes and the existing specifications.
- Fix as you see fit, possibly using a new context depending on the situation. If using an agent, it SHALL ask the user when faced with conflicts.


## Installation

LeanSDD aims at being as being as simple as possible. It features no CLI tool, and only a limited amount of configuration in your AI agent that you'll perform yourself.


## UI

When the dev agent allows it, LeanSDD comes with two personae (modes, skills etc.):
- "Spec Author (LeanSDD)" (slug: lsdd-author)
- "Spec Critic (LeanSDD)" (slug: lsdd-critic)

And the above slash-commands guiding each workflow.

| Agent | Approach | Documentation |
|---|---|---|
| RooCode, Cline, Kilo | Modes | [UI](UI-modes.md) |
| Claude Code | Skills | [UI](UI-Claude.md) |
| Codex | Slash-commands only | [UI](UI-slash.md) |
| Web chats | Self-contained prompt | [UI](UI-web.md) |
