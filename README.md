# Hermes Agent Skills, Notes & Memory

This repository is your personal knowledge base for Hermes Agent workflows, skills, and persistent notes. It serves as:
- A library of documented skills and procedures
- A place to store personal notes and reminders
- A memory store for context across sessions
- A collection of templates and best practices

## Structure

```
hermes-agent-skills-notes/
├── README.md              # This file
├── docs/                  # Documentation and guides
│   ├── skills/           # Skill documentation
│   ├── notes/            # Personal notes
│   └── memory/           # Persistent reminders
├── templates/             # Reusable templates
│   ├── skill-template.md # Template for new skills
│   ├── note-template.md  # Template for notes
│   └── ...
├── workflows/             # Workflow definitions
│   ├── github/           # GitHub-related workflows
│   ├── git/              # Git workflows
│   └── ...
└── scripts/               # Helper scripts
```

## Quick Start

### Adding a New Skill
```bash
# Create a new skill directory
mkdir -p skills/<skill-name>

# Create the skill documentation
cd skills/<skill-name>
cat > SKILL.md << 'EOF'
---
name: <skill-name>
description: "What this skill does"
version: 1.0.0
author: t2thev
---

# <skill-name>

<Your skill documentation here>
```

### Adding a Note
```bash
# Create a new note
echo "# My Note Title

<Your notes here>" > docs/notes/my-note.md
```

### Adding to Memory
```bash
# Store persistent memory
echo "Your memory item here" > docs/memory/persistent-reminder.md
```

## Skills Library

This repository will contain documented skills organized by category:

- **GitHub & Git** - Repository management, PR workflows, code review
- **Home Assistant** - Smart home device control and automation
- **Browser Automation** - Web scraping, form filling, data extraction
- **Development** - Debugging, testing, code review workflows
- **Productivity** - Notes, tasks, meeting management
- **Data Science** - Analysis, visualization, machine learning
- **And more...**

## Usage with Hermes Agent

When working with Hermes, you can:
1. Load skills from this repo using `skill_view()`
2. Reference templates in your workflows
3. Store notes and memory for future sessions
4. Document new skills discovered during tasks

## Contributing

Feel free to add new skills, update existing ones, and organize your knowledge base as you grow.

## License

Personal use only - this is your knowledge base.
