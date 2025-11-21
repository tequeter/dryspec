# LeanSDD in RooCode

## Features

### Modes

Create the Spec Author and Spec Critic modes.

They differ in goals, entry expectations, tool-use patterns, and guarantees.

#### The Author and Critic have opposite responsibilities toward change generation

##### Author Mode → generates new spec material

* Writes/modifies `req-*`, `sub-*`, `architecture.md`, glossary, etc.
* Asks questions to gather missing requirements/background.
* May propose refactors (merging/splitting files) to improve cohesion.
* Uses repo tools for discovery (`git grep`, `ls docs/`, etc.).
* Reads diffs **only** to understand “what changed in previous step.”

##### Critic Mode → does NOT author specifications unless explicitly asked

* Primary output is *diagnostics*, *inconsistency detection*, and *suggested edits*.
* Never proactively creates files or expands text.
* Provides **minimal, safe, reversible** diffs or rewritten files *only when the user asks for the fix*.
* Treats spec files as objects to evaluate, not to extend.

#### Their interaction style is different

##### Author Mode Interaction

* Asks many questions
* Navigates ambiguity
* Suggests missing requirements / missing tests
* Can reshape structure (e.g. split subsystem, move requirement)
* High-initiative

##### Critic Mode Interaction

* Does not ask clarifying questions unless absolutely necessary
* Short, dense, mechanical, deterministic
* Reports inconsistencies
* Low-initiative, high-precision

#### Their failure modes differ — and need to be prevented differently

##### Author Mode threats

* Becoming chatty
* Over-specifying
* Adding fluff
* Inventing API details
* Generating too much text
  → Must be constrained *against verbosity*, *against scope creep*, and *toward concision and structural discipline*.

##### Critic Mode threats

* Fixing things without being asked
* Overwriting files unexpectedly
* Mixing review with authorship
* Misinterpreting diffs
  → Must be constrained *against autonomous edits* and *toward dry, precise assessments*.


### Slash-commands

Create the listed slash-commands as a mean to guide the user through the matching workflow. For RooCode, "Load LeanSDD" means switching to the appropriate mode.

### Clean context

Analyze carefully the provided workflows. For all tasks that require a "clean", "empty, or "cleared" context, the agent SHALL use the `new_task` tool with the appropriate mode and instructions.

## Expected output

### Documentation file

Create or update the file `UI-modes.md` with the following structure:

- A sample session for each workflow. Ellide as much as possible to keep it short.
  - Write consistently "Spec Author" and "Spec Critic".
  - Write "in a sub-task" (or similar), not "with clean context" - that's what the user will see in RooCode's UI.
- Available slash-commands, when to use and what they do (assume the user already read `README.md`, keep it DRY).
- Available modes, same thing.

### Modes file

Create or update the file `packages/modes/leansdd-modes.yaml` in the RooCode "export mode" format, which is as follows:

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
