# DrySpec in RooCode

## Goal

Package DrySpec into reusable mode and prompts. Once installed in RooCode, they SHALL be usable in other projects without access to the DrySpec repo and files.

## Features

### DrySpec mode

DrySpec in RooCode comes with a single "DrySpec" mode. Its system prompt:

- SHALL implement the shared DrySpec knowledge, Specification index rules, and
  prompt-design constraints defined in `build/instructions.md`.
- SHOULD NOT contain workflow-specific instructions; workflows are described in
  the slash-commands below.

When you design the `roleDefinition` for this mode, assume the runtime context
described in `build/instructions.md`.

### Other modes

Additionally, you SHALL use the built-in "Ask" mode to read and summarize sizable chunks of code. This lets the user configure an expensive model for specification work and a cheap model with a huge context for code exploration.

### Slash-commands

Create these slash-commands as a means to guide the user through the corresponding workflow:

- `/dryspec-init-existing` (Brownfield)
- `/dryspec-init-new` (Greenfield)
- `/dryspec-change` (Spec-driven change)
- `/dryspec-reconcile` (Code-driven change)

Each slash-command prompt SHALL:

- Make use of the `switch_mode` tool rather than expecting or asking the user to select the DrySpec mode.
- Assume the Git and context-management rules specified in the "Workflows" section of `SPECIFICATION.md`:
  - Start from, or explicitly check for, a clean Git state where that matters.
  - Use staging (`git add`) as the primary mechanism to protect specification changes, and remind the user that Git operations SHOULD be explicitly confirmed.
  - Encourage minimal, step-scoped contexts and the use of fresh sub-tasks rather than accumulating too much unrelated context.
- Stay DRY with respect to the mode’s `roleDefinition`: reference the relevant DrySpec sections (e.g. Brownfield/Greenfield/Specification-Driven Change/Code-Driven Change) and their intent, but do not restate them in full; instead, summarize what the agent needs to do in that workflow and rely on the mode’s `roleDefinition` for shared rules.
- Make use of the `update_todo_list` tool to keep track of the tasks ahead.

### Clean context

For all workflow tasks that require a "clean", "empty", or "cleared" context, the agent SHALL use the `new_task` tool with the appropriate mode and instructions.

The `new_task` prompt SHALL contain all the necessary information to carry out its task, but not more. The subtask has no access to the parent task's context.

## Expected output

### Documentation file

Create or update the file `UI-RooCode.md` with the following structure:

- A sample session for each workflow. Elide as much as possible to keep it short.
  - Agents in RooCode can spawn sub-tasks, users can only start a new chat.
  - Write "in a sub-task" (or similar), not "with clean context" - that's what the user will see in RooCode's UI.
  - Mention the actions that the user must do, such as completing their Constitution. If these actions do not require usage of a LLM, mark them clearly outside of any chat.
  - Use numbered lists for ease of reading.
- Available slash-commands, when to use and what they do (assume the user already read `SPECIFICATION.md`, keep it DRY).
- Available modes, same thing.

The recipient of this file is the end-user. Do not include information only useful for the agent.

### Modes file

Create or update the file `packages/RooCode/dryspec-modes.yaml` in the RooCode "export mode" format, which is as follows (example):

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
        - fileRegex: \.md$  # Second element is the options object
          description: Markdown files only
      - browser
  - slug: another-mode
    name: Another Mode
    # ... other properties
```

Be sure to allow writing to all Markdown files in the project.

### Slash commands

Create or update the files `packages/RooCode/dryspec-*.md` in RooCode's format, which is (example):

```markdown
---
description: Comprehensive code review focusing on security and performance
---

Please perform a thorough security review of the selected code:

1. **Authentication & Authorization**
...
```

The user MAY provide additional instructions after the slash-command, they will be appended to the command's prompt body. Demonstrate this in the UI file.

## Your Task

Follow the common instructions in `build/instructions.md` and the RooCode
constraints above, then:

- Review `SPECIFICATION.md` and this file for RooCode-specific details not already
  covered in `build/instructions.md`.
- Design the prompts for the slash-commands and modes so they support the
  DrySpec workflows (Brownfield/Greenfield/spec-driven change/code-driven
  change) while staying DRY with respect to the shared `roleDefinition`.
- Output the requested files.
