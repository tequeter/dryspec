---
prompt: As a RooCode expert, carefully study the provided context (README.md) and instructions and think hard to generate UI-modes.md. Do not bother with leansdd-modes.yaml.
---

# LeanSDD UI (RooCode Modes)

LeanSDD in RooCode is delivered as two modes plus a small set of slash-commands. Modes define *persona + guarantees*; slash-commands define *workflow entry points*. “Clean/empty context” steps in workflows are implemented in RooCode by running the step **in a sub-task** with the appropriate mode.  

## Sample sessions (short, elided)

Below, `User>` are your messages, `Agent>` are RooCode replies.
Anything labeled “(in a sub-task)” means you open a new RooCode task in the stated mode.

### 1) Brownfield workflow (`/lsdd-brownfield`)

**Goal:** reverse-engineer specs from an existing codebase, then critique them.

**Session**

User> `/lsdd-brownfield`
Agent (Spec Author)> “Let’s start. Is your Git state clean? If yes, paste your Constitution draft from AGENTS.md / CLAUDE.md.”
User> “Git clean. Constitution draft: …”
Agent (Spec Author)> “Great. I’ll scan `docs/` and repo structure. I may open sub-tasks per subsystem.”

**(in a sub-task — Spec Author)**
Agent> “Deep-diving subsystem `foo/`: purpose, boundaries, key internal components, and any implicit requirements I can *suggest*. Questions: …”
User> Answers…
Agent> “Findings: Subsystem summary… Possible requirements (not fully authored)… Architecture notes… Glossary candidates…”

**Back to main task — Spec Author**
Agent> “I’ll update `docs/sub-foo.md`, `docs/architecture.md` (if needed), and init `docs/req-*.md` with placeholders from findings. Please confirm or edit these summaries: …”
User> Confirms/adjusts.

**(in a sub-task — Spec Author)**
Agent> “Now let’s flesh out `docs/req-bar.md`. Questions to make behavior unambiguous: …”
User> Answers…
Agent> Produces concise requirement + high-signal tests.

User> “Specs staged. Please critique.”
Agent (Spec Critic)> “Reviewing staged diffs vs specs. Inconsistencies:

1. req-bar says X but sub-foo implies Y.
2. Missing error case for Z.
   Suggested minimal edits: … (no files modified).”
   User> “Apply fix 1 and 2.”
   Agent (Spec Critic)> Provides a minimal patch / rewritten sections.
   User> stages & commits.

### 2) Greenfield workflow (`/lsdd-greenfield`)

**Goal:** create a new project by writing the Constitution, then switch to change workflow for real work.

**Session**

User> `/lsdd-greenfield`
Agent (Spec Author)> “Greenfield = Constitution only. Please provide:

1. one-paragraph product description,
2. your code best practices,
   and I’ll format the Specification Index block.”
   User> Provides text.
   Agent (Spec Author)> Returns tight Constitution section.
   User> stages it.

User> “Now we need first feature X.”
Agent (Spec Author)> “Use `/lsdd-change` for any non-trivial work.”

### 3) Specification-Driven Change (`/lsdd-change`)

**Goal:** update specs first (Requirements → Architecture/Subsystems), then implement with a coder agent.

**Session**

User> `/lsdd-change Add ‘dry-run’ flag to CLI`
Agent (Spec Author)> “We’ll do specs first. Do requirements change? Let’s clarify desired user behavior and errors: …”
User> Answers…

**(in a sub-task — Spec Author)**
Agent> Updates/creates relevant `docs/req-*.md` (+ tests) and maybe glossary.
Agent> “Here are the exact edits. Please review.”
User> Confirms.

**(in a sub-task — Spec Author)**
Agent> “Now technical layer: which subsystems/architecture need updates? I’ll read `git diff` if you changed requirements.”
Agent> Updates `docs/sub-*.md` / `docs/architecture.md` minimally.
User> stages spec.

User> “Critique the staged spec.”
Agent (Spec Critic)> Lists inconsistencies / missing cases, suggests minimal fixes.
User> “Apply those fixes.”
Agent (Spec Critic)> Supplies patch.
User> stages again.

User> “Implement based on staged specs.”
(You switch to your Coder mode/agent and point it at staged specs.)
User> commits.

### 4) Code-Driven Change (`/lsdd-code-change`)

**Goal:** you already changed code (or specs didn’t seem needed). LeanSDD checks spec/code alignment.

**Session**

User> “I have code staged for a bugfix.”
User> `/lsdd-code-change`
Agent (Spec Critic)> “I’ll compare staged code to existing specs. Findings:

* Spec claims A but code now does B.
* req-foo missing edge case C revealed by fix.
  Options: update req-foo or revert behavior. No edits made yet.”
  User> “Update req-foo accordingly.”
  Agent (Spec Author)> “Switching to Spec Author to update requirements.”
  Agent (Spec Author)> Produces minimal req update.
  User> stages & commits.

## Slash-commands

These commands are thin workflow entry points; they mainly switch you into the right mode and checklist.  

* **`/lsdd-brownfield`**
  Use when starting LeanSDD on an existing repo. Walks you through: clean Git → Constitution → subsystem discovery (often in sub-tasks) → architecture/glossary consolidation → requirements fleshing → critique → commit.

* **`/lsdd-greenfield`**
  Use for brand-new projects. Produces/finishes the Constitution and points you to `/lsdd-change` for any real feature work.

* **`/lsdd-change`**
  Default for non-trivial work. Guides spec-first iteration:

  1. Requirements (+ glossary) in a sub-task if needed
  2. Architecture/subsystems in a sub-task
  3. stage specs
  4. optional critique (Spec Critic)
  5. implement with coder agent against staged specs.

* **`/lsdd-code-change`**
  Use when code is already staged (or specs weren’t updated first). Runs Spec Critic to find divergences and suggest minimal spec fixes.

## Modes

LeanSDD provides two RooCode modes. They are intentionally asymmetric.  

### Spec Author (LeanSDD) — `lsdd-author`

**When to use**

* Writing or updating any spec file (`docs/req-*.md`, `docs/sub-*.md`, `docs/architecture.md`, `docs/glossary.md`).
* Any `/lsdd-*` workflow phase that *generates* spec content.

**What it does**

* High-initiative spec writing & refactoring for cohesion/DRYness.
* Asks clarifying questions to remove ambiguity.
* Uses repo tools to discover context.
* Reads diffs only to understand previous spec changes.

**Guarantees / constraints**

* Optimizes for concision, no fluff, no invented APIs.
* Updates specs destructively instead of adding “patch notes.”
* Never edits the Constitution unless explicitly asked.

### Spec Critic (LeanSDD) — `lsdd-critic`

**When to use**

* Reviewing staged specs vs repo reality.
* The critique step in `/lsdd-brownfield` and `/lsdd-change`.
* The main mode for `/lsdd-code-change`.

**What it does**

* Low-initiative, high-precision inconsistency detection.
* Produces diagnostics and suggested minimal edits.
* Does **not** expand or create spec text unless you ask for a fix.

**Guarantees / constraints**

* No autonomous rewrites or file creation.
* Avoids speculative design; focuses on contradictions, gaps, unsafe ambiguity.
* Only asks clarifying questions if absolutely required.

<!-- markdownlint-disable-file MD036 -->
