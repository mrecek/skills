# Handoff Skill — First-Time Setup

The handoff skill saves session context documents to a configurable directory. Without configuration, it defaults to `~/.handoffs/`. To customize the save location (e.g., to an Obsidian vault), create a config file.

## Quick Setup

```bash
mkdir -p ~/.config/handoff
cat > ~/.config/handoff/config.json << 'EOF'
{
  "handoff_dir": "~/vaults/work-vault/workspace/handoffs"
}
EOF
```

Replace the path with your preferred directory.

## Config Schema

The config file lives at `~/.config/handoff/config.json`:

```json
{
  "handoff_dir": "/absolute/or/tilde/path/to/handoffs"
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `handoff_dir` | string | yes | Base directory for handoff documents. Supports `~` for home directory. |

## Example Configurations

**Obsidian vault:**
```json
{
  "handoff_dir": "~/vaults/work-vault/workspace/handoffs"
}
```

**Plain directory:**
```json
{
  "handoff_dir": "~/.handoffs"
}
```

**Work laptop with custom path:**
```json
{
  "handoff_dir": "~/Documents/session-handoffs"
}
```

## Directory Structure

Once configured, handoff documents are organized as:

```
<handoff_dir>/
├── my-app/
│   ├── 2026-02-26-1430-auth-refactoring.md
│   └── 2026-03-01-0900-api-migration.md
├── infra-tools/
│   └── 2026-02-28-1100-terraform-modules.md
└── general/
    └── 2026-03-15-1600-claude-setup.md
```

The project subdirectory is auto-detected from the git remote or repository root name.
