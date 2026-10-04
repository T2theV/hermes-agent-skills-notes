# Hermes Agent Skills, Notes & Memory

This repository is your personal knowledge base for Hermes Agent workflows, skills, and persistent notes. It serves as:
- A library of documented skills and procedures
- A place to store personal notes and reminders
- A memory store for context across sessions
- A collection of templates and best practices

## Repository Details

- **Repository**: https://github.com/T2theV/hermes-agent-skills-notes
- **Branch**: `main` (only branch - all commits go here)
- **Purpose**: Store skills, notes, and persistent memory for Hermes Agent

## Directory Structure

```
hermes-agent-skills-notes/
├── README.md              # This file - main documentation
├── DOCUMENTATION.md       # Detailed structure documentation
├── .gitignore             # Git ignore patterns
├── docs/                  # Documentation, guides, notes, memory
│   ├── skills/           # Skill documentation (SKILL.md files)
│   ├── notes/            # Personal notes organized by topic
│   └── memory/           # Persistent reminders across sessions
├── templates/             # Reusable templates
│   ├── skill-template.md # Template for creating skills
│   └── note-template.md  # Template for creating notes
├── workflows/             # Workflow definitions
│   ├── github/           # GitHub workflows (PRs, issues, etc.)
│   └── git/              # Git workflows (branching, commits)
└── scripts/               # Helper scripts and utilities
```

## Quick Start

### For Hermes Agent (when I'm working):

**I automatically manage this repository. Just tell me what to store:**

1. **Add a new skill**: "Document a new skill for X"
2. **Add a note**: "Create a note about Y"
3. **Store memory**: "Remember this important detail Z"
4. **Create templates**: "Make a template for W"

### Manual Usage (if needed):

```bash
# Add a skill
mkdir -p docs/skills/<skill-name>
cat > docs/skills/<skill-name>/SKILL.md << 'EOF'
---
name: <skill-name>
description: "What this skill does"
version: 1.0.0
author: T2theV
---

# <skill-name>

<Your skill documentation here>
EOF

# Add a note
cat > docs/notes/<topic>.md << 'EOF'
# <Note Title>

<Your notes here>
EOF

# Store memory
cat > docs/memory/<reminder>.md << 'EOF'
# <Reminder Title>

<What to remember>
EOF
```

## Git Workflow

- **Branch**: `main` only (no feature branches)
- **Commit format**: `<type>: <description>`
  - `feat:` New skill or feature
  - `fix:` Bug fix
  - `docs:` Documentation update
  - `chore:` Maintenance tasks
- **Push**: Automatic when I make changes

## Usage Examples

### With Hermes Agent:

```python
# Store a skill
from hermes_tools import write_file
write_file("docs/skills/git-workflow.md", "# Git Workflow\n...")

# Add a note
write_file("docs/notes/shopping.md", "# Shopping List\n...")

# Store persistent memory
write_file("docs/memory/preferences.md", "# Preferences\n...")
```

## What Gets Stored

### Skills
- Documented workflows and procedures
- Tool usage guides
- Best practices
- Troubleshooting guides

### Notes
- Personal reminders
- Meeting notes
- Project documentation
- Shopping lists
- Ideas and thoughts

### Memory
- Setup instructions
- Important configurations
- Preferences
- Critical reminders

## Access

- **GitHub**: https://github.com/T2theV/hermes-agent-skills-notes
- **Clone**: `git clone https://github.com/T2theV/hermes-agent-skills-notes.git`

## Contributing

Feel free to:
- Add new skills you discover
- Organize notes by topic
- Create templates for common tasks
- Update documentation

## License

Personal use only - this is your knowledge base.

---

**Current Commits:**
- `9c4fdf0` chore: add .gitignore and directory structure documentation
- `2c1d349` Initial commit: skills, notes & memory repository
