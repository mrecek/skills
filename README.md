# skills

Custom AI agent skills designed to work with agent harnesses that support skills, such as Claude Code, Cursor, and OpenCode.

Use the [Vercel Skills CLI](https://github.com/vercel/skills) to install and manage skills.

## Install

```bash
# Install a specific skill globally for a specific agent
npx skills add mrecek/skills --skill <skill-name> -g -a <agent>

# Examples
npx skills add mrecek/skills -s greenfield -g -a claude-code
npx skills add mrecek/skills -s yt-transcript -g -a claude-code
npx skills add mrecek/skills -s drawio-diagrams -g -a cursor
```

## Available Skills

| Skill | Type | Description |
|-------|------|-------------|
| `greenfield` | Behavioral | First-principles design mode for building or rewriting documentation, skills, and code |
| `drawio-diagrams` | Tool | Read, validate, and edit draw.io diagrams at the XML level |
| `yt-transcript` | Tool | Format YouTube transcripts into markdown using yt-dlp with author chapters |
