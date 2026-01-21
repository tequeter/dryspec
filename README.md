# DrySpec

Drive your development with Just Enough specifications.

## Why?

- Get a thinking partner throughout design.
- Specify what matters most and will remain updated.
- Provide critical context to your other prompts and tasks.

## How is it different?

Unlike OpenSpec, SpecKit, BMAD, etc., DrySpec focuses on:

- Shorter is better (faster to read, fewer tokens).
- High-level only (don't repeat the code).
- Yet fully specified within that scope.
- Small, high-signal, orthogonal Markdown files loaded on demand.
- Opinionated specification structure.
- Full support for agile and existing codebases.
- Destructive updates (history is in Git).

## Next steps

- Skim through the [specification](SPECIFICATION.md) and the documentation for your [runtime](#supported-runtimes).
- Install the package in your runtime.
- Create your project [constitution](SPECIFICATION.md#constitution).
- Go on with the [workflows](SPECIFICATION.md#workflows).

## Supported runtimes

| AI runtime           | Approach              | Usage                 | Packages                              |
| -------------------- | --------------------- | --------------------- | ------------------------------------- |
| RooCode, Cline, Kilo | Mode and /-commands   | [UI](UI-RooCode.md)   | [Packages](packages/RooCode/)         |
| Claude Code          | Skills                | (not implemented)     |                                       |
| Codex                | Self-contained prompt | [UI](UI-Codex.md)     | [Packages](packages/Codex/dryspec.md) |
| Web chats            | Self-contained prompt | (Try Codex's prompt?) |                                       |

## Recommended models

I'm very pleased with GPT 5.x High for this task. In the Claude family I'd try Opus with *think harder* or *ultrathink*.

## Installation

- No CLI tool.
- Drop the package files in your runtime's folder(s) - see your runtime's package above for details.
