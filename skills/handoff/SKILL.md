---
name: handoff
description: "Preserve context and create comprehensive handoff documents for seamless session transitions. This skill is invoked only via the /handoff slash command — do not trigger it automatically."
---

# Handoff

Capture and document session state for seamless handoffs. Create comprehensive handoff documents that allow future sessions to continue work with full context.

## When to Use

Use `/handoff` when you need to:
- Preserve context before running out of context window space
- Checkpoint progress on a complex task
- Transition between work sessions
- Save/store current progress explicitly

### Examples

**Proactive context preservation:**
> "I notice we've been working on this refactoring for a while and the context is getting substantial. Let me use handoff to save our progress before continuing."

**Explicit save request:**
> User: "Let's save where we are - I need to take a break"
> Assistant: "I'll use handoff to document our current progress and state so we can resume seamlessly later."

**Approaching context limits:**
> "We've made significant progress debugging this issue. Before we potentially lose context, let me capture everything we've learned."

**Natural transition points:**
> User: "Great, the API endpoints are done. Now let's work on the frontend."
> Assistant: "Perfect - the API work is complete. Let me document what we accomplished before we shift focus."

## Handoff Document Template

Generate a structured markdown file following this template:

```markdown
# Handoff: [Topic/Task Title]

**Date**: [YYYY-MM-DD HH:MM]
**Session Duration Context**: [Brief note on scope of work covered]
**Project**: [detected project name]
**Repository**: [git remote URL, or "Local only" if no remote]
**Working Directory**: [Full path]

## 🎯 Original Objective
[Clear statement of what the user wanted to accomplish]

## 📍 Current Status
[One-paragraph summary of where things stand]

### Completed
- [x] [Task 1 with brief details]
- [x] [Task 2 with brief details]

### In Progress
- [ ] [Current task with state details]

### Pending
- [ ] [Remaining task 1]
- [ ] [Remaining task 2]

## 🔧 Key Changes Made

### Files Modified
| File | Change Type | Summary |
|------|-------------|----------|
| `path/to/file` | Modified/Created/Deleted | Brief description |

### Code Highlights
[Any critical code snippets or patterns that are important to remember]

## 🔀 Git Context
- **Branch**: [current branch name]
- **Last Commit**: [short hash + subject]
- **Uncommitted Changes**: [summary — "Clean working tree" or list of staged/unstaged files]

## 💡 Important Decisions & Rationale
1. **[Decision]**: [Why this choice was made]
2. **[Decision]**: [Why this choice was made]

## 🐛 Issues & Solutions
| Issue | Resolution | Notes |
|-------|------------|-------|
| [Problem encountered] | [How it was fixed] | [Any caveats] |

## 🧠 Key Insights
- [Important discovery or learning]
- [Useful context for future work]

## ⚠️ Warnings & Gotchas
- [Things to be careful about]
- [Known issues or limitations]

## 🚀 Next Steps (Priority Order)
1. [Immediate next action with specific details]
2. [Following action]
3. [Subsequent action]

## 📎 Relevant Resources
- [Links, file references, documentation consulted]

## 💬 Resume Prompt
[A ready-to-use prompt that can be given to continue this work, including essential context]
```

## Configuration

The handoff save location is configured via a JSON file at `~/.config/handoff/config.json`.

If no config file exists, handoffs save to `~/.handoffs/` as a default. On first use without a config, inform the user that they can customize the save location and point them to `references/setup.md` in this skill for instructions.

**Reading the config:**
1. Check if `~/.config/handoff/config.json` exists
2. If it exists, read it and use the `handoff_dir` value as the base directory
3. If it does not exist, use `~/.handoffs/` as the base directory

## File Storage Protocol

1. **Determine Base Directory**: Read from config (see above)

2. **Project Detection**: Determine the project name using this chain:
   1. Try `git remote get-url origin` — extract the repo name (last path segment, strip `.git` suffix)
   2. Fallback: use the git root directory name (`basename $(git rev-parse --show-toplevel)`)
   3. Final fallback: if not in a git repo, use `general`

3. **Directory Structure**: Store files at `<base_dir>/<project>/<yyyy-mm-dd>-<HHmm>-<topic>.md`
   - `<base_dir>`: From config or fallback
   - `<project>`: Detected project name (see above)
   - `<yyyy-mm-dd>`: Today's date
   - `<HHmm>`: Current time in 24-hour format (hours and minutes, no separator)
   - `<topic>`: A kebab-case slug (2-5 words) describing the main topic
   - Create the directory if it doesn't exist (`mkdir -p`)

4. **Filename Examples**:
   - `~/vaults/work-vault/workspace/handoffs/my-app/2026-02-26-1430-auth-refactoring.md`
   - `~/.handoffs/infra-tools/2026-02-26-0915-terraform-modules.md`

## Quality Standards

- **Completeness**: Someone with no prior context should understand the full situation
- **Actionability**: Next steps should be specific enough to act on immediately
- **Accuracy**: Double-check file paths, code references, and technical details
- **Conciseness**: Be thorough but avoid unnecessary verbosity
- **Resume-Ready**: The document should make resumption effortless

## Execution Steps

1. Determine the current working directory, today's date and time, and git context (branch, remote, status, last commit)
2. Detect the project name using the project detection chain
3. Read the config file to determine the base directory (or use fallback)
4. Ensure the target directory exists (`mkdir -p <base_dir>/<project>/`)
5. Analyze the full conversation context
6. Identify a clear, descriptive topic slug
7. Generate the handoff document following the template
8. Write the file to the appropriate location
9. Confirm the file was created and provide the path
10. Optionally provide a brief summary of what was captured

## Self-Verification Checklist

Before finalizing, verify:
- [ ] All modified files are listed
- [ ] Current state is accurately represented
- [ ] Git context (branch, uncommitted changes) is captured
- [ ] Project directory is correct
- [ ] Next steps are clear and prioritized
- [ ] Resume prompt would give sufficient context
- [ ] No sensitive information is inadvertently included
- [ ] File path follows the correct convention

Be proactive about capturing comprehensive context. When in doubt about whether to include something, include it — it's better to have extra context than to lose important details.
