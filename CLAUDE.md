# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This repository manages DevOps site changes using the **OpenSpec** workflow system. It's configured for spec-driven change management where each change goes through a structured process: proposal → design → tasks → implementation.

The repository contains:
- `openspec/` – OpenSpec configuration, changes, and specs
- `.claude/` – Custom Claude Code commands and skills for OpenSpec workflows

## OpenSpec Workflow

OpenSpec is a CLI tool for managing changes through artifacts. The workflow uses a **spec-driven** schema with these artifacts:
1. **proposal.md** – What and why of the change
2. **design.md** – How the change will be implemented
3. **tasks.md** – Implementation steps (checklist)

### Key Commands

**OpenSpec CLI commands** (requires `openspec` installed globally):
- `openspec new change <name>` – Create a new change directory
- `openspec status --change <name>` – Show change status
- `openspec list` – List all changes
- `openspec instructions <artifact> --change <name>` – Get instructions for creating an artifact
- `openspec instructions apply --change <name>` – Get implementation instructions

**Custom Claude Code skills** (available via `/` commands):
- `/opsx:propose <name-or-description>` – Create a new change and generate all artifacts
- `/opsx:apply [change-name]` – Implement tasks from a change
- `/opsx:explore` – Enter explore mode for investigating problems
- `/opsx:archive` – Archive a completed change

These skills are defined in `.claude/commands/opsx/` and `.claude/skills/openspec-*`.

## Directory Structure

```
openspec/
├── config.yaml          # OpenSpec configuration (schema: spec-driven)
├── changes/             # Active changes (each in its own subdirectory)
│   └── archive/        # Archived changes
└── specs/              # Schema definitions (empty – using default spec-driven)

.claude/
├── commands/opsx/      # Custom command definitions
│   ├── propose.md
│   ├── apply.md
│   ├── explore.md
│   └── archive.md
└── skills/openspec-*/  # Skill implementations
    ├── openspec-propose/
    ├── openspec-apply-change/
    ├── openspec-explore/
    └── openspec-archive-change/
```

## Working with Changes

### Creating a Change
1. Use `/opsx:propose add-feature-name` or describe the change.
2. The skill creates `openspec/changes/add-feature-name/` with `.openspec.yaml`.
3. It generates `proposal.md`, `design.md`, and `tasks.md` following the schema.

### Implementing a Change
1. Use `/opsx:apply add-feature-name` (or omit name if only one active change).
2. Read context files (proposal, design, tasks) to understand requirements.
3. Work through tasks in `tasks.md`, marking each as completed (`- [ ]` → `- [x]`).
4. Keep changes minimal and scoped to each task.
5. When all tasks are complete, archive the change with `/opsx:archive`.

### Exploring and Archiving
- Use `/opsx:explore` to investigate problems or clarify requirements before proposing.
- Use `/opsx:archive` to move a completed change to `openspec/changes/archive/`.

## Development Notes

- **No build/lint/test setup** – This repository is focused on change management, not code execution.
- **OpenSpec CLI required** – Ensure `openspec` is installed and available in PATH.
- **Changes are self-contained** – Each change directory contains all artifacts needed for implementation.
- **Context and rules** – When creating artifacts, follow the `context` and `rules` from `openspec instructions` but do NOT include them in the output files.

## Common Tasks

1. **Start a new change**: `/opsx:propose "description of change"`
2. **Implement an existing change**: `/opsx:apply [change-name]`
3. **Check change status**: `openspec status --change <name>`
4. **List all changes**: `openspec list`
5. **Archive completed change**: `/opsx:archive`

## Important Constraints

- Always read dependency artifacts before creating new ones.
- When implementing, update task checkboxes immediately after completion.
- If a task is unclear, pause and ask for clarification.
- Keep code changes minimal – don't refactor beyond what's required by the task.
- Use `openspec instructions apply --json` to get the current state and context files.