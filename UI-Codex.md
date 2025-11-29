# LeanSDD in Codex CLI

LeanSDD is available in Codex CLI as the `/prompts:leansdd` slash-command. It provides a LeanSDD-aware specification coach and reviewer that helps you design, refine, and critique lightweight Agile specifications for your project, all within a single conversation.

You can invoke it from any repository where you want to apply LeanSDD-style specs; the assistant understands the core LeanSDD concepts and adapts to the files and Constitution in your current project.


## What `/prompts:leansdd` knows

`/prompts:leansdd` is preloaded with LeanSDD’s key ideas:

- **Constitution**
  - Assumes your project has a “Constitution” file (typically `AGENTS.md`, `CLAUDE.md`, or similar) that is always in context.
  - Expects the Constitution to contain a short project description, code best practices, and a project-specific “Specification index”.
  - Treats that Constitution (and its index) as the single source of truth for where specs live and how big they should be.
  - Will not change the Constitution unless you explicitly ask it to.

- **Specification structure**
  - Understands the standard LeanSDD spec layout:
    - `docs/specs/fr-*.md` for Functional Requirements (FRs).
    - `docs/specs/nfr.md` for Non-Functional Requirements (NFRs).
    - `docs/specs/glossary.md` for domain terms.
    - `docs/specs/architecture.md` for global architecture and technical choices.
    - `docs/specs/sub-*.md` for major internal subsystems.
  - Uses the generic Specification index (file roles and size limits) as a fallback when your Constitution does not define its own, but always defers to project-specific rules.

- **Agile-spec constraints**
  - Prioritizes DRY, concise, short, and dense specs.
  - Keeps each file cohesive and loosely coupled to others.
  - Uses sparse, high-signal acceptance scenarios, each with a stable identifier suitable for linking from tests.
  - Avoids incremental “patch note” sections; prefers destructive edits that leave each file as a clean, up-to-date spec.
  - Treats detailed internal interfaces and tests (beyond acceptance scenarios) as out of scope for specs; those are left to code and test files.

- **File roles and boundaries**
  - Keeps FRs focused on user-visible behavior or public library interfaces.
  - Keeps NFRs focused on cross-cutting qualities (performance, reliability, scalability, safety, testability).
  - Uses the Glossary for non-obvious domain terms, not for UI copy.
  - Uses Architecture and Subsystem specs for structural and technical choices, without going down to method signatures or code.
  - Knows that detailed data models, external contracts (APIs, message formats), and UI wording belong in separate docs; it will not create or change such files unless you clearly ask it to.

- **Git and context assumptions**
  - Assumes Git is used to protect spec changes (e.g., via `git add`).
  - Can work from staged diffs (`git diff --cached`), file contents, or snippets you paste into the chat.
  - Encourages small, focused contexts: a single FR file, a couple of related specs, or a modest diff at a time.


## How to use `/prompts:leansdd`

Start by running `/prompts:leansdd` in Codex CLI to load the LeanSDD-aware assistant for your current repository. Its first reply is a short confirmation of its role and capabilities and an invitation to describe what you want to work on; it does not proactively scan or summarize your specs or code until you ask.

After that initial handshake, just send normal messages describing your task (for example, “Help me draft a Constitution for…” or “Review these FRs for LeanSDD issues”). `/prompts:leansdd` does not create modes or subtasks; everything happens in a single conversation where you can paste files, diffs, or summaries as needed and steer the work with follow-up questions.

Below are common task types and example interactions.


### Draft or refine a Constitution

Use this when bootstrapping LeanSDD for a project or tightening an existing Constitution.

**Example**

- **User**

  ```text
  /prompts:leansdd
  ```

- **Assistant (abridged)**

  ```text
  I’m your LeanSDD specification coach for this repo. I can help with Constitutions, FR/NFR/Glossary/Architecture/Subsystem specs, spec/code reconciliation, and LeanSDD-style critique.

  Tell me what you want to work on first.
  ```

- **User**

  ```text
  Help me draft a Constitution for a small CLI that syncs local notes to a remote server. I want brief code best practices and a reasonable spec index for a Python project.
  ```

- **Assistant (abridged)**

  ```text
  I’ll draft a concise Constitution with:
  - A one-paragraph description of the CLI and why it exists.
  - A short list of Python best practices aligned with your goals.
  - A project-specific Specification index based on LeanSDD’s FR/NFR/Glossary/Architecture/Subsystem structure.

  Before I write it, a couple of clarifications:
  1) Do you expect multiple subsystems (e.g., CLI, sync engine, storage), or keep it as one subsystem for now?
  2) Are there any non-negotiable practices you already apply (type hints, strict linting, etc.)?
  ```


### Author or update FR/NFR/Glossary/Architecture/Subsystem specs

Use this when you want to create or refine individual spec files while staying within LeanSDD boundaries.

After you have run `/prompts:leansdd` in this repo, you can ask for focused spec help like this.

**Example (FR authoring)**

- **User**

  ```text
  Draft a new `docs/specs/fr-note-sync.md` for my notes CLI. It should cover the user starting a sync, handling connectivity errors, and showing a summary of changes. Keep it short and include a few acceptance scenarios with stable IDs.
  ```

- **Assistant (abridged)**

  ```text
  I’ll create a concise FR focused on note sync:
  - 2–3 short context paragraphs from the user’s point of view.
  - Bullets for main behaviors and error conditions.
  - A small set of high-signal scenarios with IDs like `#happy-path` and `#network-failure`.

  Clarifications:
  1) Should sync be automatic on every edit, or explicit on demand?
  2) Do you require any performance constraints (e.g., max sync time)?
  ```


### Reconcile specs and code

Use this when you have code changes or failures and want to see how they line up with existing specs.

**Example**

- **User**

  ```text
  I’ve added a retry-on-failure feature to the sync engine. Here’s the diff (staged). Compare it with `docs/specs/fr-note-sync.md` and tell me what parts of the FR are now outdated or incomplete. Suggest updates but don’t rewrite the whole file unless necessary.
  ```

- **Assistant (abridged)**

  ```text
  I’ll:
  - Summarize the current behavior described in `docs/specs/fr-note-sync.md`.
  - Compare it to the diff to identify mismatches (e.g., new retry behavior not mentioned in the FR, updated error messages).
  - Propose targeted edits to the FR, keeping it concise and destructive (no patch notes).

  If you paste the current content of `docs/specs/fr-note-sync.md` and the diff, I can point out exactly which bullets and scenarios to update.
  ```


### Critique existing specs for LeanSDD alignment

Use this when you want a LeanSDD-style review rather than new content.

**Example**

- **User**

  ```text
  Review `docs/specs/fr-login.md` and `docs/specs/nfr.md` for LeanSDD issues. Point out where they’re too verbose, mixing concerns, or leaking data models/UI copy. Suggest concrete, high-level improvements rather than rewriting everything.
  ```

- **Assistant (abridged)**

  ```text
  I’ll look for:
  - Overly long context sections or repetitive bullets.
  - Architecture or subsystem details leaking into FRs or NFRs.
  - Data models or UI wording that belong in separate docs.
  - Incremental “update” sections instead of clean destructive edits.

  I’ll return:
  - A short list of concrete issues per file.
  - Specific suggestions on what to trim, move, or split, and which sections would benefit from new or clearer acceptance scenarios.
  If you’d like, I can then help you apply those suggestions in a follow-up step.
  ```


## Conversation flow and scope

- Run `/prompts:leansdd` once to load the LeanSDD assistant for a new conversation; its first reply is a brief welcome and an invitation to describe your task, not an automatic tour of your specs.
- You control the scope: it works best when you focus on a small set of files or a specific diff rather than your entire codebase at once.
- As your specs evolve, you can either keep working in the same conversation or start a fresh `/prompts:leansdd` session for a new phase of work (for example, drafting the Constitution, later adding FRs/NFRs, then doing reconciliation or critique).
- At any time, you can paste spec files, code snippets, or `git diff` output; the assistant will adapt to what you provide and ask focused clarifying questions only when needed.
