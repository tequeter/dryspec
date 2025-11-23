# LeanSDD in RooCode

## Goal

Package LeanSDD into reusable mode and prompts. Once installed in RooCode, they SHALL be usable in other projects without access to the LeanSDD repo and files.

## Features

### LeanSDD mode

LeanSDD in RooCode comes with a single "LeanSDD" mode. Its system prompt:

- SHALL contain all the LeanSDD static knowledge and instructions, except as noted below.
- SHALL NOT duplicate the "Specification index" section, already loaded in the LLM context through the AGENTS.md mechanism.
- SHOULD NOT contain workflow-specific instructions.

When you design the `roleDefinition` for this mode, assume that at runtime the agent will only see:

- The Constitution from the host repo (via `AGENTS.md` / `CLAUDE.md`, etc.), which already embeds the generic "Specification index".
- The current project’s specs and code (but not this LeanSDD repo).

Therefore, the `roleDefinition` MUST:

- Be self-contained with respect to LeanSDD (do not rely on the README being present at runtime).
- Explicitly summarize, in your own words, the LeanSDD concepts and constraints from `README.md` that are **not** covered by the Specification index table alone, in particular:
  - The notion of the Constitution (location, contents, always-loaded nature) and the rule that the agent SHALL NOT edit it unless explicitly asked by the user.
  - The additional "Agile specifications" constraints (DRY/concise, loose coupling/high cohesion of spec files, separation of internal semantics vs UI wording, sparse/high-signal acceptance scenarios, no incremental spec patches, no proposed changes inside specs), including:
    - That detailed internal interfaces and tests beyond acceptance are out of scope for the specs.
    - That minor design decisions are explicitly left to the coder agent and its human operator.
    - That LeanSDD is optimized for incremental, agile change rather than a waterfall process (you MAY briefly allude to this contrast without reproducing the FAQ).
  - The per-file roles and semantics for FR, NFR, Glossary, Architecture, and Subsystem specs, including what belongs in each, what is out of scope, and the key naming/identifier rules for FRs. In particular, encode that:
    - For FRs: application FRs focus on major user-visible requirements, library FRs focus on the public interface; acceptance scenarios MAY use a Gherkin-like structure; each scenario SHOULD have a stable identifier suitable for linking from executable tests (for example, `fr-login.md#happy-path`); and FRs MAY record priority using the MoSCoW method.
    - For Architecture and Subsystems: if there is no global `docs/architecture.md`, the relevant architectural and technical choices MUST be captured in subsystem specs; subsystem specs MAY describe local architecture/technical choices and SHOULD name the major classes and functions in their internal interface between subsystems, but SHALL NOT go down to method signatures or function arguments, and SHALL NOT contain code or pseudo-code.
  - The boundaries described in "Other files" (data models, external contracts, UI wording) and the fact that the agent SHALL NOT create or update such files unless explicitly instructed, and SHALL avoid stuffing these details into FR/NFR/Architecture/Subsystem specs.
  - Cross-repository linking via canonical URLs, where multi-repo systems may reference read-only documentation in other repos.
  - The global assumptions from "Workflows" about Git usage (clean working tree, staging as protection, user-confirmed Git operations) and about minimal, step-scoped context management.

Do **not** paste large chunks of `README.md` verbatim into the `roleDefinition`. Instead, compress and paraphrase these points so that the mode remains short, dense, and DRY while still giving the agent enough static knowledge to operate without access to the LeanSDD repo.

### Other modes

Additionally, you SHALL the built-in "Ask" mode to perform code deep-dives. This lets the user configure an expensive model for specification work and a cheap model with a huge context for code exploration.

### Slash-commands

Create these slash-commands as a mean to guide the user through the corresponding workflow:

- `/lsdd-init-existing` (Brownfield)
- `/lsdd-init-new` (Greenfield)
- `/lsdd-change` (Spec-driven change)
- `/lsdd-reconcile` (Code-driven change)

Each slash-command prompt SHALL:

- Make use of the `switch_mode` tool rather than expecting or asking the user to select the LeanSDD mode.
- Assume the Git and context-management rules specified in the "Workflows" section of `README.md`:
  - Start from, or explicitly check for, a clean Git state where that matters.
  - Use staging (`git add`) as the primary mechanism to protect specification changes, and remind the user that Git operations SHOULD be explicitly confirmed.
  - Encourage minimal, step-scoped contexts and the use of fresh sub-tasks rather than accumulating too much unrelated context.
- Stay DRY with respect to the mode’s `roleDefinition`: reference the relevant LeanSDD sections (e.g. Brownfield/Greenfield/Specification-Driven Change/Code-Driven Change) and their intent, but do not restate them in full; instead, summarize what the agent needs to do in that workflow and rely on the mode’s `roleDefinition` for shared rules.

### Clean context

For all workflow tasks that require a "clean", "empty, or "cleared" context, the agent SHALL use the `new_task` tool with the appropriate mode and instructions.

The `new_task` prompt SHALL contain all the necessary information to carry out its task, but not more. The subtask has no access to the parent task's context.

## Expected output

### Documentation file

Create or update the file `UI-RooCode.md` with the following structure:

- A sample session for each workflow. Elide as much as possible to keep it short.
  - Agents in RooCode can spawn sub-tasks, users can only start a new chat.
  - Write "in a sub-task" (or similar), not "with clean context" - that's what the user will see in RooCode's UI.
- Available slash-commands, when to use and what they do (assume the user already read `README.md`, keep it DRY).
- Available modes, same thing.

### Modes file

Create or update the file `packages/RooCode/leansdd-modes.yaml` in the RooCode "export mode" format, which is as follows (example):

```yaml
customModes:
  - slug: docs-writer
    name: 📝 Documentation Writer
    description: A specialized mode for writing and editing technical documentation.
    roleDefinition: You are a technical writer specializing in clear documentation.
    whenToUse: Use this mode for writing and editing documentation.
    groups:
      - read
      - - edit  # First element of tuple
        - fileRegex: \.(md|mdx)$  # Second element is the options object
          description: Markdown files only
      - browser
  - slug: another-mode
    name: Another Mode
    # ... other properties
```

| Field            | Documentation                                                                                                           |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------- |
| `description`    | A short, user-friendly summary of the mode's purpose. Keep this concise and focused on what the mode does for the user. |
| `roleDefinition` | Defines the core identity and expertise of the mode. This text is placed at the beginning of the system prompt.         |
| `whenToUse`      | Provides guidance for Roo's automated decision-making, particularly for mode selection and task orchestration.          |
| `groups`         | Available groups: "read", "edit", "browser", "command", "mcp".                                                          |

The system prompts (`roleDefinition`) must be self-contained, with all necessary instructions.

### Slash commands

Create or update the files `packages/RooCode/lsdd-*.md` in RooCode's format, which is (example):

```markdown
---
description: Comprehensive code review focusing on security and performance
---

Please perform a thorough security review of the selected code:

1. **Authentication & Authorization**
...
```

| Field         | Documentation                                                                                                              |
| ------------- | -------------------------------------------------------------------------------------------------------------------------- |
| `description` | A short, user-friendly summary of the command's purpose. Keep this concise and focused on what the mode does for the user. |

The user MAY provide additional instructions after the slash-command, they will be appended to the command's prompt body. Demonstrate this in the UI file.

## Your Task

As an expert Prompt Engineer:

- Review very carefully <README.md>, <build/instructions.md>, and this file.
  - Special instructions for Codex when using `shell_command` to read files:
    - Read <README.md> in two tool calls of 150 lines each as it exceeds 10 kB.
- Do not explore files other than those explicitly listed, they are not relevant for this task.
- This repo does not have a AGENTS.md or similar, do not try to find one.
- Consider what instructions might be needed to overcome the listed "Challenges".
- Design the prompts for the slash-commands and modes to support the LeanSDD workflows. Think very hard.
- Output the requested files.
