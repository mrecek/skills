# Skills Directory

Each subdirectory here is a skill. The `npx skills` CLI discovers skills by scanning this directory for subdirectories containing a `SKILL.md` file.

## Skill Structure

```
skill-name/
├── SKILL.md              # Required — frontmatter + instructions
├── script                # Optional — executable for deterministic tasks
├── scripts/              # Optional — bundled helper scripts
├── references/           # Optional — docs loaded into context as needed
└── assets/               # Optional — templates, icons, fonts
```

## SKILL.md Frontmatter

Every `SKILL.md` requires YAML frontmatter with `name` and `description`:

```yaml
---
name: my-skill
description: "What this skill does and when to use it."
---
```

### Quoting Rules

The `npx skills` CLI parses frontmatter with [gray-matter](https://github.com/jonschlinkert/gray-matter). If parsing fails, the skill is **silently skipped** — no warning, no error.

**Always quote the `description` value** if it contains any of these YAML-special characters:

| Character | Example |
|-----------|---------|
| `:` | `"Handles formats: json, csv, xml"` |
| `#` | `"Generates C# bindings"` |
| `{` `}` | `"Outputs {key: value} pairs"` |
| `[` `]` | `"Accepts [optional] flags"` |
| `>` `\|` | `"Pipe output \| filter results"` |

When in doubt, quote it. Unquoted descriptions with colons are the most common cause of skills not appearing in `npx skills install`.

## How Discovery Works

The CLI clones the repo and scans these paths in order:

1. Root `SKILL.md` (mono-skill repos — returns immediately unless `--full-depth`)
2. `skills/` subdirectories (this directory)
3. `.claude/skills/`, `.github/skills/`, and other agent-specific paths
4. Deep fallback: recursive scan up to 5 levels, skipping `node_modules`, `.git`, `dist`, `build`, `__pycache__`

Skills are deduplicated by `name` — if two skills share a name, only the first one found is kept.

## Testing Locally

**Personal skills directory** — copy or symlink into `~/.claude/skills/`:
```bash
cp -r skills/my-skill ~/.claude/skills/my-skill
```
Claude picks these up automatically. Run `/reload-plugins` to refresh during a session.

**Plugin dir flag** — load for a single session:
```bash
claude --plugin-dir ./skills/my-skill
```

## Skill Types

| Type | Has `script`? | Example |
|------|---------------|---------|
| Tool skill | Yes | `yt-transcript`, `drawio-diagrams` |
| Behavioral skill | No | `greenfield` |

Tool skills bundle executable scripts for deterministic tasks. Behavioral skills modify how Claude approaches work — they contain only instructions in `SKILL.md`.
