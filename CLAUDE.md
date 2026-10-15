@AGENTS.md

## Claude Code adapter

- Treat the imported `AGENTS.md` as the canonical shared instructions.
- Do not create agent teams or dynamic workflows unless the user explicitly requests them.
- Use project skills from `.claude/skills/`, which points to the shared `.agents/skills/` directory.

