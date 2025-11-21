# LeanSDD

## Discussion

The specifications are a golden guardrail for the AI dev agent that would otherwise get lost during the implementation phase.

Good specifications:

- Must be concise, short, and dense: AI tends to be overly verbose and that's not required for a decent coder model. Huge specifications eat up the context, are harder to maintain, and produce little value.
- Must not leave room for architectural improvisation. Yet must not be over-specified (see previous point) - good coding models will handle minor decisions properly without having everything written down prior.
- Must be DRY: duplicated information is harder to keep consistent when updating the spec.
- Must be made of loosely coupled files loaded into the LLM's context as needed. Each file must have high cohesion.
- Must distinguish internal semantics (error types, conditions) from external presentation such as UX vernacular.
- Must include sparse, high-signal, canonical test cases to complement the concise prose.
- Must have a size limit on each file to force concision, test sparseness, and module cohesion.
- Must not produce incremental "specification patches" (neither as a "notes" chapter nor as extra files). They will become outdated quickly and bloat the LLM context. Instead, refactor destructively the existing specifications as required, without losing existing detail.

The workflows and the specifications must be optimized for the incremental changes that come with agile practices, rather than clinging to the obsolete waterfall model.

Existing practice (SpecKit, BMAD, ...) seems to focus on the wrong problems: how to create more text (input specs, various lessons learned post-implementation,  ...) and shovel it around. It's just a busywork trap, creating more structure instead of better structure. They completely miss the point of having lean and DRY specifications that can evolve ahead of the code.

OpenSpec is interesting but spends a lot of effort & tokens duplicating what Git already does well, while it falls short of specifying what goes in the specifications and critiquing the end result for coherency.

## Specification layers

You will have these layers:

1. Formal specification documenting functional Requirements.
2. Formal specification documenting technical aspects.
3. Implicit specification as executable tests, internal interfaces etc.
4. Transient specifications with the agent for the duration of the coding session.

LeanSDD only cares about 1 and 2.

## Specification structure

### Constitution

The Constitution is always loaded into the context, for all agents, so it is part of `AGENTS.md`, `CLAUDE.md`, or a similar file. It contains:

1. A short description of the program in one paragraph. What the software does (features), why, what problem it solves.
2. Code best practices.
3. A specification index provided below.

The user provides the Constitution (the description is quick to write, and the best practices are supposedly already identified before the project).

The agent is forbidden to edit the Constitution without an explicit request from the user.

#### Specification index

> You may read the software specifications here:
> - `docs/req-*.md` for user-visible features or library interface.
> - `docs/glossary.md` (optional).
> - `docs/architecture.md` (optional).
> - `docs/sub-*.md` for the major internal software components.

### Requirements

The software must have requirements documented as `docs/req-*.md`. This is the What and Why.

For an application, do specify the major, user-visible requirements.

For a library, do specify the interface.

Do not specify anything else as requirements.

Requirements typically have:

1. A description from the point of view of the user.
2. High-level test cases, again from the point of view of the user. May use the Gherkin structure.

Both combined must describe the expected behavior unambiguously, including the error conditions.

### Glossary

The software SHOULD have a `docs/glossary.md` defining the non-obvious terms used throughout the specification and code.

### Architecture

The software MAY have a general architecture and technical choices document as `docs/architecture.md`. This is the How.

If not specified, that information must be provided in the subsystems.

### Subsystems

We call subsystems the major internal software components (modules, packages, crates, ...).

Each subsystem must be specified as `docs/sub-*.md`.

The file must at least contain a description of what the subsystem does and of its major components.

It MAY additionally:
1. Describe the architecture and technical choices.
2. Name the major classes, functions etc. part of its internal interface (from one subsystem to another).

It SHALL not further describe the internal interface (methods, function arguments etc.). That information can be obtained in more effective ways (codebase index) and is subject to change in every refactor.

It SHALL not include subsystem code, pseudo-code, or anything of that sort.

### Other files

The user MAY create other files under `docs/`.


## Workflows

The workflows below feature instructions to alter the Git state (`git stage`, `git commit`). The agent MAY execute them, but the user SHOULD carefully OK each such attempt.

NB: the user MAY of course perform intermediate commits instead of just staging the changes.

### Brownfield

For an existing project,
- Start with a clean Git state.
- Complete your Constitution.
- Starting with an empty context, load LeanSDD, then identify the Subsystems with the help of the agent (clear the context between each).
- Likewise for the Architecture.
- And for Glossary entries and Requirements.
- Stage your changes.
- Clear and load LeanSDD again, then ask the agent to critique your specification, looking for inconsistencies. Fix as needed.
- Commit.

### Greenfield

Creating a new project (greenfield deployment) just means filling in the Constitution as described above.

Suggested next steps are:
- Creating a Glossary and specifying the Architecture.
- Describing Requirements and Subsystems.

Those are covered by the Specification-Driven Change workflow below.


### Specification-Driven Change

This is the main workflow, and it SHOULD be used for all non-trivial changes.

The agent SHALL interact with and guide the user through creating or updating the specification as per the above framework.

If useful for the task at hand, the agent SHOULD gather context using the provided tools and ask as many clarifying questions as necessary to the user.

For the user, the change workflow should be as follows:
- Start with a clean Git state.
- If requirements need to be updated, start with an empty context, load LeanSDD, and update the Requirements and possibly the Glossary with the help of the agent.
- Clear the context again, load LeanSDD, and update the Architecture and/or the Subsystems with the help of the agent. Tell the agent to look at `git diff` if you changed anything in the previous step.
- Once satisfied with the spec update, stage it for protection (`git add`).
- Depending on your confidence level, clear the context and have LeanSDD critique your changes. Stage again.
- Implement the change with a Coder agent, pointing it at the staged specification changes.
- Commit.


### Code-Driven Change

This workflow is useful when either the user skipped the specification part, or for a bugfix that didn't seem to warrant touching the specifications.

Either way,
- Start with your code changes (and nothing else) staged in Git.
- Ask LeanSDD (critique mode, if there is such a thing) to look for inconsistencies between your staged code changes and the existing specifications.
- Fix as you see fit, possibly using a new context depending on the situation.
- Commit.

## Installation

LeanSDD aims at being as being as simple as possible. It features no CLI command, and only a limited amount of configuration in your AI agent that you'll perform yourself.

## UI

When the dev agent allows it, LeanSDD comes with two personae (modes, skills etc.):
- "Spec Author (LeanSDD)" (slug: lsdd-author)
- "Spec Critic (LeanSDD)" (slug: lsdd-critic)

And `/lsdd-brownfield`, `/lsdd-greenfield`, `/lsdd-change`, `/lsdd-code-change` slash-commands to guide the user through the workflows.

| Agent | Approach | Documentation |
|---|---|---|
| RooCode, Cline, Kilo | Modes | [UI-modes.md] |
| Claude Code | Skills | [UI-Claude.md] |
| Codex | Slash-commands only | [UI-slash.md] |
| Web chats | Self-contained prompt | [UI-web.md] |

## TODO

- Can LeanSDD be recursive?
