---
description: Code-Driven Change workflow to reconcile staged code changes with DrySpec specs.
---

You are orchestrating the DrySpec Code-Driven Change workflow in RooCode.

High-level goals:

1. Start from staged code changes and detect inconsistencies with existing DrySpec specs.
2. Decide whether to adjust specs, adjust code, or split the work.
3. Keep specs concise, accurate, and aligned with actual behavior.

Follow these rules and steps:

0. At the beginning of the workflow, use the `update_todo_list` tool to capture the intended steps (from the list below) as a checklist, and keep it updated as you make progress or adjust the plan.

1. **Select modes and context**
   - Use `switch_mode` to ensure you are in the `DrySpec` mode in the main task.
   - Use `switch_mode` to move into the built-in `ask` mode within `new_task` sub-tasks when you need to read and summarize sizable chunks of code or diffs (large-context code or diff analysis).

2. **Check Git and clarify the intent**
   - Verify that the user has staged only code changes (no spec files) for this workflow; if spec files are staged, summarize the situation and ask whether they should be unstaged or handled via `/dryspec-change` instead.
   - Ask the user what bug, feature, or refactor the staged changes are meant to address and whether there are any known spec mismatches already.

3. **Understand the staged diff (in sub-tasks)**
   - Start a `new_task` in `ask` mode focused on the staged diff (and possibly nearby files) with instructions to:
     - Summarize behavior changes in short, high-signal bullets.
     - Identify which existing FRs, NFRs, Glossary entries, Architecture sections, or Subsystem specs might be affected.
     - Flag any behavior that appears undocumented or contradicts the current specs.
   - If the diff spans multiple subsystems or features, consider multiple sub-tasks, each scoped to one area, to keep contexts small and focused.

4. **Decide on spec vs code adjustments**
   - Back in `DrySpec` mode, use the sub-task summaries to:
     - List the spec files that would need updates to match the staged behavior.
     - Call out any changes that look like accidental spec drift rather than intentional behavior.
   - Ask the user whether the specs should be updated to match the code, the code should be adjusted to match the specs, or the work should be split (for example, by creating a follow-up task).

5. **Update specs when appropriate**
   - If the decision is to adjust specs:
     - Use a `new_task` in `DrySpec` mode to apply minimal, destructive edits to the relevant FR/NFR/Glossary/Architecture/Subsystem files so they accurately describe the staged behavior.
     - Keep each file short, cohesive, and within its role; avoid adding patch notes or speculative future behavior.
     - Do not introduce detailed data models, external contract definitions, or UI copy; if they are implicated by the change, describe only the high-level impact.
   - Summarize the spec deltas clearly.

6. **Protect and critique**
   - Propose staging the updated spec files separately from the code (if they are not staged yet) and ask for explicit confirmation before running any Git commands.
   - Start a `new_task` in `DrySpec` mode to critique the combined staged state (code + specs) by:
     - Checking for remaining inconsistencies between behavior and documentation.
     - Ensuring that specs remain concise and that responsibilities are in the right files.
   - Report any remaining issues and options (e.g., adjust code, adjust specs again, split into multiple changes).

7. **Summarize outcomes**
   - Provide a brief summary of:
     - What the staged code changes do.
     - How the specs were updated (or why they were left unchanged).
     - Any follow-up tasks or open questions.
   - Remind the user to commit code and spec changes together when appropriate.
