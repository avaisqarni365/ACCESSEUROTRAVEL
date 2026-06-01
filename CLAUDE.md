# ACCESS

## Project Purpose
<!-- Describe what this project does -->

## Key Conventions
- Reusable prompts go in `.claude/prompts/`
- Bug records go in `.claude/bug-history/`
- Session transcripts go in `.claude/sessions/`

# Project AI Operating Rules

Always use context engineering before every output.

Before coding:
- Read memory.md
- Read .claude/sessions/latest-session.md if available
- Check .claude/prompts/
- Check .claude/bug-history/
- Understand current sprint from .claude/sprint/

For every response:
- Explain what context was used
- Improve the project structure if needed
- Save reusable decisions into memory.md
- Save important prompts into .claude/prompts/
- Save bugs and fixes into .claude/bug-history/

At session close:
- Create a session summary in .claude/sessions/
- Update memory.md
- List completed tasks, open tasks, bugs, next steps, and useful prompts