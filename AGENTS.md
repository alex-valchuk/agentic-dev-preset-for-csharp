# Agentic Dev Preset for C#/.NET

Generic behavior defaults for AI coding agents. Domain knowledge, architecture decisions, build commands, and project-specific conventions belong in the consuming repository's own `AGENTS.md`, not here.

## Behavior

- Do not rewrite code "to look nicer" without a request.
- Do not add comments "for the future", `TODO`s, or `#region` blocks without a request.
- When patching, list every anchor you intend to use and verify that each one exists before editing.
- Split multi-file changes into explicit stages (step 1, step 2, step 3) and report progress between them.
- Follow the existing pattern in the codebase. If a base class, abstraction, or registration mechanism already exists, extend it instead of inventing a parallel structure.
- Prefer deterministic, idempotent scripts for repetitive multi-file edits; verify their output afterwards.
- After changes, run the project's build and tests and report the exact commands and results.
- Do not edit generated files, lock files, or installer/packaging artifacts unless the user explicitly asks.

## Detailed rules

Load only the instructions relevant to the current task:

- `instructions/workflow.md`
- `instructions/powershell.md` - PowerShell syntax and safe patching notes
- `instructions/solutions.md`
- `instructions/repository-pattern.md`
- `instructions/localization.md`
- `instructions/ui/xaml-formatting.md`
- `instructions/ui/prism-navigation.md`

Reusable tooling lives in `agents/`, `commands/`, `skills/`, `plugins/`, and `scripts/`.
