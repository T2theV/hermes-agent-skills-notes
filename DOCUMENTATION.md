# Directory Structure Documentation

This document describes the directory structure of the Hermes Agent skills, notes, and memory repository.

## Root Level

| Directory/File | Purpose |
|---------------|---------|
| `README.md` | Main documentation and quick start guide |
| `.gitignore` | Git ignore patterns for the workspace |
| `docs/` | Documentation, guides, notes, and memory |
| `templates/` | Reusable templates for skills, notes, workflows |
| `workflows/` | Workflow definitions and procedures |
| `scripts/` | Helper scripts and utilities |

## docs/

Contains all documentation, notes, and persistent memory.

### docs/skills/
- **Purpose**: Document skills and procedures discovered or created
- **Files**: Individual `.md` files for each skill
- **Example**: `docs/skills/git-workflow.md`, `docs/skills/github-auth.md`

### docs/notes/
- **Purpose**: Personal notes and reminders
- **Files**: Organized by topic or date
- **Example**: `docs/notes/shopping.md`, `docs/notes/2026-10-04.md`

### docs/memory/
- **Purpose**: Persistent reminders that survive across Hermes sessions
- **Files**: Critical information to remember
- **Example**: `docs/memory/setup-instructions.md`, `docs/memory/preferences.md`

## templates/

Reusable templates for creating content consistently.

### templates/skill-template.md
- **Purpose**: Template for creating new skills
- **Includes**: YAML frontmatter, description, metadata, usage examples

### templates/note-template.md
- **Purpose**: Template for creating structured notes
- **Includes**: Title, date, tags, content sections

## workflows/

Workflow definitions and procedures for common tasks.

### workflows/github/
- **Purpose**: GitHub-related workflows
- **Files**: PR workflows, issue management, repo management
- **Example**: `workflows/github/pr-workflow.md`, `workflows/github/issues.md`

### workflows/git/
- **Purpose**: Git operations and best practices
- **Files**: Branching strategies, commit conventions, pull strategies
- **Example**: `workflows/git/branching.md`, `workflows/git/commit.md`

## scripts/

Helper scripts for automation and utilities.

- **Purpose**: Reusable scripts for common tasks
- **Example**: `scripts/setup-git.sh`, `scripts/backup-notes.sh`

## Usage with Hermes

When working with Hermes Agent:

1. **Add a skill**: Write to `docs/skills/<skill-name>.md`
2. **Add a note**: Write to `docs/notes/<topic>.md`
3. **Store memory**: Write to `docs/memory/<reminder>.md`
4. **Create templates**: Write to `templates/<type>.md`

## Git Workflow

- **Branch**: `main` (only branch)
- **Commit message format**: `<type>: <description>`
- **Types**: `feat`, `fix`, `docs`, `chore`, `refactor`
