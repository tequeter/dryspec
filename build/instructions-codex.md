# LeanSDD in Codex CLI

## Goal

Package LeanSDD into a single reusable Codex CLI prompt. Once installed in Codex CLI, it SHALL be usable in other projects without access to the LeanSDD repo and files.

## Features

### LeanSDD prompt `/prompts:leansdd`

LeanSDD in Codex CLI is exposed only as the `/prompts:leansdd` slash-command.
Its prompt body:

- SHALL implement the shared LeanSDD knowledge, Specification index rules, and
  prompt-design constraints defined in `build/instructions.md`.
- SHALL NOT define or assume any explicit "modes", "subtasks", or multi-command
  workflows. Codex CLI does not support those features; all behavior MUST be
  driven by the user’s instructions within a single ongoing conversation.

When you design the `/prompts:leansdd` prompt body, assume the runtime context
described in `build/instructions.md` ("Runtime context") and that the user’s
current message MAY ask for help with tasks such as drafting or refining a
Constitution, authoring or updating specifications, critiquing specs, or
reconciling specs and code.

Therefore, the `/prompts:leansdd` prompt body MUST:

- Treat the user’s request as the driver for the interaction. The prompt SHALL
  instruct the agent to:
  - Ask a short, focused list of clarifying questions when the goal,
    constraints, or target specs are ambiguous instead of assuming missing
    context on its own.
  - Interpret the user’s description of the task (for example, drafting specs,
    critiquing them, or reconciling specs and code) and apply the relevant
    LeanSDD knowledge accordingly, without inventing additional tasks or
    hidden workflows.
  - Adapt its behavior to the user’s stated task while remaining within the
    same conversation and without spawning "subtasks" or switching "modes".

### Capabilities covered by `/prompts:leansdd`

Because Codex CLI exposes only a single prompt for LeanSDD, the `/prompts:leansdd` body MUST give the agent dense, reusable knowledge for all of the following areas; the user will decide when and how to use them:

- **Constitution knowledge**
  - Understand what belongs in a Constitution (project overview, coding norms, Specification index) and why it must stay concise and high-signal.
  - Recognize that the Constitution is always loaded and SHOULD NOT be edited unless the user explicitly asks.
  - Be able to explain how the Constitution and the Specification index relate to the individual spec files and their roles.

- **New-project specifications**
  - Understand how to characterize a system in terms of FRs, NFRs, Glossary entries, Architecture notes, and Subsystem docs, even when no specs exist yet.
  - Know what "minimal but sufficient" initial specs look like: small files, coarse-grained FRs, sparse acceptance scenarios, and placeholder structure rather than detailed internal design.
  - Be able to suggest reasonable file names and boundaries that respect LeanSDD size and role constraints, while leaving detailed design to later.

- **Existing-codebase specifications**
  - Understand how directory structure, modules, and public APIs map to subsystems and architectural decisions.
  - Be able to describe behavior and responsibilities at the right level of abstraction (what the system does, not how every function works) and turn those into candidate FR/NFR/Architecture/Subsystem specs.
  - Avoid rewriting code as prose or dumping low-level technical detail into the specs.

- **Change impact and reconciliation**
  - Understand that meaningful behavior changes should eventually be reflected both in specs and in code, and be able to reason about where spec updates are likely needed when behavior changes.
  - Help the user reason about mismatches between current code and current specs without assuming that either side is automatically correct.
  - Emphasize destructive, coherent edits to specs rather than patch notes, keeping file roles and concision intact.

- **Critique and review**
  - Understand typical failure modes (too verbose, wrong file, hidden assumptions, incremental edits, data models leaking into specs, UI copy embedded in FRs, etc.).
  - Offer concrete, DRY suggestions and clarifying questions when reviewing specs, instead of silently rewriting them.
  - Apply the "Challenges" from `build/instructions.md` when explaining why something should be simplified or made more explicit.

The prompt SHALL describe the agent’s abilities and constraints as domain knowledge and behavioral patterns, not as explicit workflows or command sequences. The agent MUST NOT invent a long multi-step process unless the user explicitly requests help organizing the work.

### Context management in Codex CLI

Codex CLI does not expose "modes" or "subtasks". The `/prompts:leansdd` prompt body MUST therefore:

- Encourage the agent to keep contexts small and focused by:
  - Working on one clearly defined concern at a time (for example, a single FR file, a small set of related specs, or a limited diff).
  - Summarizing prior findings before moving on to a new aspect, instead of repeatedly rereading the same files.
- Encourage the agent to ask the user to narrow scope when requests are too broad for a single conversation, and to suggest splitting the work into smaller, sequential tasks handled within the same chat.

## Expected output

### Slash-command prompt

Create or update the file `packages/Codex/leansdd.md` in Codex CLI’s prompt format:

```markdown
---
description: LeanSDD specification coach and reviewer
---

You are an expert LeanSDD assistant…
```

The user MAY provide additional instructions after the slash-command (for example, `/prompts:leansdd` followed by a brief task description). These additional instructions will be appended to the command's prompt body; design the prompt so that it treats the appended text as the primary task description and adapts accordingly.

The body of `packages/Codex/leansdd.md` SHALL implement the requirements described in the sections above.

### UI documentation

Create or update the file `UI-Codex.md` with the following structure:

- A short introduction explaining that LeanSDD is available in Codex CLI through the `/prompts:leansdd` slash-command, and that the command provides a LeanSDD-aware specification coach and reviewer.
- A concise overview of what `/prompts:leansdd` "knows" about LeanSDD: Constitutions, FR/NFR/Glossary/Architecture/Subsystem specs, Agile-spec constraints, file roles and boundaries, and Git/context assumptions.
- Clear guidance on the main kinds of tasks the user can ask for, such as:
  - Drafting or refining a Constitution.
  - Authoring or updating FR/NFR/Glossary/Architecture/Subsystem specs.
  - Reconciling specs and code when they appear to diverge.
  - Critiquing existing specs for alignment with LeanSDD rules.
- For each type of task, one short example of how a user might invoke `/prompts:leansdd` and what kind of answer to expect. Keep examples compact (a single user message plus a brief, high-signal assistant reply), and avoid long, step-by-step workflows.
- A brief reminder that `/prompts:leansdd` does not create modes or subtasks in Codex CLI; the user stays in a single conversation and can paste or reference files as needed.

The recipient of `UI-Codex.md` is the end-user of Codex CLI. Do not include information only useful for the agent’s internal behavior or for prompt engineering.

## Your Task

Follow the common instructions in `build/instructions.md` and the Codex CLI
constraints above, then:

- Review `README.md` and this file for Codex-specific details not already
  covered in `build/instructions.md`.
- Design the `/prompts:leansdd` prompt body so that it supports all LeanSDD
  tasks (Constitution work, Greenfield/Brownfield-like specification
  authoring, specification changes, code/spec reconciliation, and critique),
  while remaining a single, reusable prompt with no explicit modes or subtask
  constructs and no built-in workflows.
- Output the requested files `packages/Codex/leansdd.md` and `UI-Codex.md`.
