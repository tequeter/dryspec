# LeanSDD in RooCode

## Features

### Modes

LeanSDD in RooCode features the following modes:

| Name in documentation and messages | Name in RooCode UI      | RooCode slug  |
| ---------------------------------- | ----------------------- | ------------- |
| Spec Author                        | "Spec Author (LeanSDD)" | `lsdd-author` |
| Spec Critic                        | "Spec Critic (LeanSDD)" | `lsdd-critic` |

Additionally, we use the built-in "ask" mode to perform code deep-dives. This lets the user configure an expensive model for Spec modes and a cheap model with a huge context for deep-dives.

### Slash-commands

Create these slash-commands as a mean to guide the user through the corresponding workflow:

- `/lsdd-brownfield`
- `/lsdd-greenfield`
- `/lsdd-change`
- `/lsdd-code-change`

### Clean context

For all workflow tasks that require a "clean", "empty, or "cleared" context, the agent SHALL use the `new_task` tool with the appropriate mode and instructions.

## Expected output

### Documentation file

Create or update the file `UI-RooCode.md` with the following structure:

- A sample session for each workflow. Elide as much as possible to keep it short.
  - Write consistently "Spec Author" and "Spec Critic".
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
    customInstructions: Focus on clarity and completeness in documentation.
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

The system prompts (`roleDefinition`) must be self-contained, with all necessary instructions.

### Slash commands

Create or update the files `packages/RooCode/lsdd-*.yaml` in RooCode's format, which is (example):

```yaml
---
description: Comprehensive code review focusing on security and performance
argument-hint: <file or directory to review>
---

# Security-First Code Review

Please perform a thorough security review of the selected code:

1. **Authentication & Authorization**
...
```

| Field           | Documentation                                                                                                              |
| --------------- | -------------------------------------------------------------------------------------------------------------------------- |
| `description`   | A short, user-friendly summary of the command's purpose. Keep this concise and focused on what the mode does for the user. |
| `argument-hint` | Further argument(s) to provide, if any. Optional.                                                                          |

## Your Task

As an expert Prompt Engineer:

- Review very carefully <README.md>, <build/instructions.md>, and this file.
- Important: do not explore files other than those explicitly listed, they are not relevant for this task.
- Consider that the "Specification Index" is intended as a user-tunable and SHOULD NOT be duplicated in the system prompts. It is guaranteed to be already loaded in the LLM context by RooCode.
- Design the prompts for the slash-commands and modes to support the LeanSDD workflows. Think very hard.
- Output the requested files.
