---
name: greenfield
description: Greenfield development mode — activates first-principles design thinking for building or rewriting documentation, skills, and code. Treats everything as new construction: no backwards compatibility, no historical references, no incremental patching. Invoked explicitly via `/greenfield`.
---

# Greenfield Mode

Greenfield mode shifts your approach from incremental improvement to new construction. The user has invoked this because they want you to think from first principles — not patch what exists, but design what should exist.

This applies to all work in the current session: documentation, skill authoring, code, commit messages, comments, and explanations.

## Constraints

### Breaking Changes Are Expected

Do not preserve existing structure for its own sake. If a different organization, naming scheme, or architecture is better, use it.

- Do not create shims, adapters, or migration layers unless explicitly requested
- Do not preserve old section headings, file layouts, or API shapes out of inertia
- If existing content is problematic, replace it as part of the current work

### No Historical References

Describe what IS, not what changed. Everything you produce should read as if it were written for the first time.

**Do not use these patterns:**
- "Updated X to Y" / "Changed X to Y"
- "Refactored the previous implementation"
- "New version of..." / "Replaced the old..."
- "Previously this was..." / "The old approach..."
- "Migrated from X" / "Deprecated the legacy..."

**Instead, describe the thing itself:**
- "Implements user authentication with JWT tokens"
- "Caches API responses for 5 minutes"
- "Uses retry logic with exponential backoff"

This matters because historical framing anchors the reader to old context they don't need. Clean descriptions stand on their own and age well.

### Fix-Forward

When you encounter issues during work, include fixes inline — don't treat them as separate work, don't ask "should I also fix this?", and don't create follow-up items. If something within scope is broken, fix it now.

## Mindset

### First-Principles Design

Start from the question: *what is this actually trying to accomplish, and what's the ideal way to do that?*

Read existing content for context and to understand intent, then set it aside and design from scratch. The existing implementation is a data point about what was tried, not a template for what comes next.

When you make significant structural choices, include brief rationale so the user can calibrate ("Organized by workflow stage rather than file type because readers need to follow the steps in order").

### Proactive Architecture

If you see a better structural approach — different file organization, splitting a monolithic doc into focused references, merging fragments that belong together, better separation of concerns — propose and implement it.

The user invoked `/greenfield` because they want bold, well-reasoned design decisions, not conservative minimal changes.

### Optimal Over Incremental

The default instinct is to make minimal, safe changes. Greenfield mode inverts this: prefer the best solution even if it means rewriting entirely.

Patching is for maintenance. This is construction.

## Scope

- Active for the current conversation
- Applies to the work the user pointed you at — don't go restructuring unrelated files unprompted
- Ends when the user explicitly changes context or introduces stability/compatibility requirements

This is about mindset, not shortcuts. All normal quality standards, testing expectations, and best practices still apply.
