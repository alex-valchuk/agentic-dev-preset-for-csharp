# Workflow: Task-based development with handoff

## Fundamentals

Each major task is represented by a separate file in the **consuming repository** at `tasks/<name>.md`. Keep task files out of the preset submodule: they are mutable work state.

The task file is the **source of truth** for its status. When switching chats or contexts, provide the new session with:

1. `AGENTS.md` (generic preset rules plus the project's own rules).
2. `tasks/<active>.md` (the current task).

Other resources — specifications, domain notes, examples — should be attached through OpenCode `references` or read on demand rather than copied into every prompt.

## `tasks/` layout

```text
tasks/
├── README.md        # index: active tasks and a one-line summary for each
├── _template.md     # template for new tasks
├── <task1>.md       # active task
├── <task2>.md       # active task (rarely, for parallel work)
└── archive/
    └── <done-task>.md   # completed tasks; keep for history
```

## Task file structure

Required sections, in this order:

1. **Header** — Status (ACTIVE/PAUSED/DONE), Created, Last updated, Owner.
2. **Goal** — the desired outcome, stated in product terms rather than technical ones.
3. **Current stage** — where the work is now (`Stage N of M`).
4. **Next stage** — what the next iteration addresses.
5. **Progress table** — `| Stage | Status | Date | Notes |`, one row per stage.
6. **Key files** — where the relevant code lives.
7. **Known issues** — what is broken, deferred, or intentionally left open.
8. **How to verify current state** — commands and manual checks.
9. **References** — links to specifications, attached via OpenCode references or read on demand.

## Updating task status

After finishing a stage, update the task file **directly with the edit tool**. Do not generate a PowerShell script just to patch a markdown file.

1. Read `tasks/<task>.md` and verify the stage's progress row is not already `DONE`. If it is, skip.
2. Change `| Stage N | TODO |` → `| Stage N | DONE | <today> | <brief description> |`.
3. Update `**Current stage:**` → `Stage N (DONE) / Next: Stage N+1`.
4. Rewrite `**Next stage:**` for stage N+1.
5. Set `**Last updated:**` to the current UTC timestamp.
6. Read the file back to verify all four anchors changed and no other content did.
