# agentic-dev-preset-for-csharp

A domain-agnostic preset for agentic development in C#/.NET repositories, built for OpenCode.

It contains only generic rules and reusable skills. It must not contain business domain knowledge, product architecture, or project-specific class names. Those stay in the consuming repository.

## Contents

```text
AGENTS.md                       # generic agent behavior defaults
opencode.jsonc.example          # configuration template for consumers
instructions/
  workflow.md                   # task-file based workflow with handoff
  powershell.md                 # PowerShell syntax and safe patching notes
  solutions.md                  # .sln platform mapping and structural changes
  repository-pattern.md         # persistence layer conventions
  localization.md               # .resx + Designer workflow
  ui/                           # optional, skip for non-UI repos
    xaml-formatting.md
    prism-navigation.md
skills/
  add-localization-key/
```

## Install as a git submodule

From the consuming repository root:

```bash
git submodule add https://github.com/alex-valchuk/agentic-dev-preset-for-csharp.git .agentic-preset
git submodule update --init --recursive
```

### 1. Add the config

Copy `opencode.jsonc.example` into your `.opencode/opencode.jsonc` and adjust the entries. The important part is ordering: preset rules first, project-specific overlay last.

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "instructions": [
    ".agentic-preset/AGENTS.md",
    ".agentic-preset/instructions/*.md",
    ".opencode/instructions/*.md"
  ],
  "skills": [".agentic-preset/skills"]
}
```

The `skills` field makes the preset's skills available; no symlink is required.

### 2. Keep mutable state outside the preset

Task files, active specs, and generated notes belong in the consuming repository (for example `tasks/`), never inside `.agentic-preset`. The submodule should stay clean and replaceable.

## Alternative: symlink mode

If you prefer not to use a submodule, clone the preset next to your project and symlink `AGENTS.md` and `.opencode` into the consuming repository root. Hide the symlinks via `.git/info/exclude`, not `.gitignore`.

## Update

```bash
git submodule update --remote .agentic-preset
```

Review the diff before committing the submodule pointer.

## License

MIT.
