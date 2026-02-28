# skills

Custom AI agent skills designed to work with agent harnesses that support skills, such as Claude Code, Cursor, and OpenCode.

Use the [Vercel Skills CLI](https://github.com/vercel/skills) to install and manage skills.

## Install

```bash
# Install a specific skill for a specific agent
npx skills add mrecek/skills --skill yt-transcript -g -a <agent>

# Examples
npx skills add mrecek/skills -s yt-transcript -g -a claude-code
npx skills add mrecek/skills -s yt-transcript -g -a cursor
```

## Available Skills

This repo currently ships a single skill.

| Skill | Description |
|-------|-------------|
| `yt-transcript` | Format YouTube transcripts using yt-dlp with author chapters as section markers |
