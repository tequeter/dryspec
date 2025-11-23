# LeanSDD in Codex CLI

## Goal

Package LeanSDD into a single reusable Codex CLI prompt. Once installed in Codex CLI, it SHALL be usable in other projects without access to the LeanSDD repo and files.

## Features

### LeanSDD prompt `/prompts:leansdd`

LeanSDD in Codex CLI is exposed only as the `/prompts:leansdd` slash-command. Its prompt body:

- SHALL contain all the LeanSDD static knowledge and instructions needed to help the user work with Constitutions and specifications, except as noted below.
- SHALL embed a verbatim copy of the "Specification index" table from `README.md` as a generic default, so that it can be used when bootstrapping projects that do not yet have a Constitution.
- SHALL make it explicit that, when a project-specific Constitution (for example in `AGENTS.md`, `CLAUDE.md`, or similar) contains its own "Specification index" section, that project-specific index is the single source of truth for file locations, roles, and size limits; in case of any discrepancy, the Constitution's index SHALL override the generic one baked into the prompt.
- SHALL NOT define or assume any explicit "modes", "subtasks", or multi-command workflows. Codex CLI does not support those features; all behavior MUST be driven by the user’s instructions within a single ongoing conversation.

When you design the `/prompts:leansdd` prompt body, assume that at runtime the agent will only see:

- The Constitution from the host repo (via `AGENTS.md` / `CLAUDE.md`, etc.), which SHOULD already embed the generic "Specification index".
- The current project’s specs and code (but not this LeanSDD repo).
- The user’s current message, which MAY ask for help with tasks such as drafting or refining a Constitution, authoring or updating specifications, critiquing specs, or reconciling specs and code.

Therefore, the `/prompts:leansdd` prompt body MUST:

- Be self-contained with respect to LeanSDD (do not rely on the README being present at runtime).
- Not reference "the LeanSDD repo" or similar - it is not accessible or even known.
- Treat the user’s request as the driver for the interaction. The prompt SHALL instruct the agent to:
  - Ask a short, focused list of clarifying questions when the goal, constraints, or target specs are ambiguous instead of assuming missing context on its own.
  - Interpret the user’s description of the task (for example, drafting specs, critiquing them, or reconciling specs and code) and apply the relevant LeanSDD knowledge accordingly, without inventing additional tasks or hidden workflows.
  - Adapt its behavior to the user’s stated task while remaining within the same conversation and without spawning "subtasks" or switching "modes".
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
  - The global assumptions from the README about Git usage (clean working tree, staging as protection, user-confirmed Git operations) and about minimal, step-scoped context management.
- Encode enough knowledge for the agent to assist with the main LeanSDD tasks without prescribing multi-step workflows, including:
  - Establishing, reviewing, and refining a project Constitution while respecting its always-loaded, "do not edit unless asked" nature.
  - Designing initial FR/NFR/Glossary/Architecture/Subsystem specs for a new or sparsely specified project, keeping skeleton contents small, cohesive, and within LeanSDD size limits.
  - Deriving or refining specifications from an existing codebase by recognizing subsystems and responsibilities from directory and module structure, public interfaces, and observable behaviors.
  - Keeping specs and code aligned by explaining how to compare current code behavior with existing specs and by highlighting where specs may need adjustment, without assuming that specs or code are always the source of truth.
  - Critiquing specs against LeanSDD rules (file roles, verbosity, boundaries, hidden assumptions, incremental patches) and suggesting concrete, DRY improvements.
  - Applying the "Challenges" from `build/instructions.md` by stressing conciseness, size limits, and clarifying questions within its own guidance instead of relying on a separate critique workflow.
- NOT mention modes or subtasks at all in the prompt body. Codex CLI has no concept of it, so it's uselessly confusing.

Do **not** paste large chunks of `README.md` verbatim into the `/prompts:leansdd` prompt body, **except** for the "Specification index" table, which you SHALL copy verbatim as described above so it is available even when the host project lacks a Constitution. For all other content, compress and paraphrase these points so that the prompt remains short, dense, and DRY while still giving the agent enough static knowledge to operate without access to the LeanSDD repo.

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

Create or update the file `packages/Codex/lsdd.md` in Codex CLI’s prompt format:

```markdown
---
description: LeanSDD specification coach and reviewer
---

You are an expert LeanSDD assistant…
```

| Field         | Documentation                                                                                                           |
| ------------- | ----------------------------------------------------------------------------------------------------------------------- |
| `description` | A short, user-friendly summary of the command's purpose. Keep this concise and focused on what `/prompts:leansdd` does. |

The user MAY provide additional instructions after the slash-command (for example, `/prompts:leansdd` followed by a brief task description). These additional instructions will be appended to the command's prompt body; design the prompt so that it treats the appended text as the primary task description and adapts accordingly.

The body of `packages/Codex/lsdd.md` SHALL implement the requirements described in the sections above.

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

As an expert Prompt Engineer:

- Review very carefully `<README.md>`, `<build/instructions.md>`, and this file.
  - Special instructions for Codex when using `shell_command` to read files:
    - Read `<README.md>` in two tool calls of 150 lines each as it exceeds 10 kB.
- Do not explore files other than those explicitly listed; they are not relevant for this task.
- This repo does not have an `AGENTS.md` or similar; do not try to find one.
- Consider what instructions might be needed to overcome the listed "Challenges" in `build/instructions.md`, especially around ignored authoring rules and clarifying questions.
- Design the `/prompts:leansdd` prompt body so that it supports all LeanSDD tasks (Constitution work, Greenfield/Brownfield-like specification authoring, specification changes, code/spec reconciliation, and critique), while remaining a single, reusable prompt with no explicit modes or subtask constructs and no built-in workflows.
- Output the requested files `packages/Codex/lsdd.md` and `UI-Codex.md`.
