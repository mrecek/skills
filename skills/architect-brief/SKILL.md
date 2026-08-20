---
name: architect-brief
description: "Architect-altitude decision briefs. Use when the user asks for a high-level or quick summary, a systems-architect read, or whether to greenlight something; when a turn ends on a decision the user must make; when the user asks what a term or a claim means; or when a summary is going to colleagues, managers, or a change board."
---

# Architect Brief

Reader calibration and the noun rules live in the user's `~/.claude/CLAUDE.md`.
This skill adds the brief *shape* on top of them, including one deliberate
override: elsewhere the conclusion opens the response, but inside this skill
the brief closes it instead — see Firing without being asked. Keep the
architecture at full strength. **Altitude** is about which nouns you use, not
how hard the ideas are allowed to be.

## Altitude

Ground level is anything the user cannot resolve without opening a file:

- **Symbols.** `load_retirement_evidence`, `deps.discover_ips`, `recovery_drill.py:457`.
- **Internal identifiers.** A01, criterion 2, "the qualification protection contract."
- **Terms coined this session.** "The production management substrate," "the storage contract."
- **Proof of work.** Line counts, file counts, test totals, "exists at exactly the recorded line count."

Lift each one to the system it belongs to. Name the component and what it does
rather than the identifier that implements it.

> **Ground:** `_bound_management_revision_sha` at `recovery_drill.py:457` feeds
> `load_committed_runtime_projection`, and `deps.discover_ips` fills `ctx.node_ips`.
>
> **Altitude:** Restore pins itself to one management-cluster revision, then
> discovers the node addresses for that revision. Both work.

Verification collapses to a clause. "Both repos are green," not the test counts.

**Done when every noun in the brief resolves for the user without opening a file
or asking what it means.**

## The brief

Open with the decision as an imperative. "Greenlight the fix." "Hold, one
blocker." "This needs deeper analysis."

Then prose with bolded lead-ins, under 300 words, carrying only what moves the
call. These are available moves rather than a checklist. Use the ones that apply
and skip the rest:

- What is actually wrong, said once, with what it causes in the real world.
- Architectural, or a leaf.
- What the fix costs, in units the user can price.
- The trend, where one exists. Is the problem class growing or collapsing?
- What is still unknown, and whether it blocks.

Close with a recommendation and its counterfactual: what the user spends by not
taking it. Commit to a call rather than laying out balanced options and handing
the decision back.

### Shape

> **Greenlight the fix. No deeper analysis needed.**
>
> **What's wrong is scoped and understood.** The plan drops the restore-cleanup
> step on the grounds that Kubernetes reaps it automatically. That's true for the
> jobs and false for the two Key Vault writer secrets the restore path creates.
> Those live outside the GitOps controller's ownership, in a namespace that
> survives handoff, so nothing reaps them. Net effect if unfixed: write
> credentials to your metrics and logs buckets sit in the cluster indefinitely
> after every restore.
>
> **Architecturally this changes nothing.** The separation thesis, the four
> deliveries, and the destroy-custody boundary all hold up. The fix is one line
> of scope, one item added to a surviving-surface list, and one false sentence
> corrected.
>
> **The class of finding has collapsed.** Earlier rounds found capabilities with
> no carrier anywhere. This round found one leaf capability with no carrier, and
> the leaf is deleting two secrets. You're now finding things the plan's own
> first action would surface for free.
>
> **My recommendation:** authorize the fix, then start without another review.
> The remaining risk isn't in the plan text anymore, it's in execution. Another
> paper review spends sessions finding leaf items that implementation surfaces
> on its own.

## Firing without being asked

When a turn hands the user a decision and they did not ask for a brief, the
detail goes first and the brief closes the turn. A long response lands in a
scrolling terminal with its tail on screen and its head already scrolled out
of view, so the brief — the part that has to register on arrival — has to be
the last thing written.

Open with the full evidence-backed detail under its own heading, `## Detail`
unless something more specific fits. File paths, line numbers, symbol names,
and evidence trails all live there, at ground level. Nothing is suppressed,
only reordered.

Close with the brief, unlabeled. Its bolded lead-ins already read as an
arrival rather than a continuation of the detail above — see Shape above for
that block on its own.

This order is about the user's own terminal, not about briefs in general. A
brief going to someone else follows Forward below instead, and stays
conclusion-first.

When the user asks for the full review, audit, or analysis, give that alone.

## Translate

When the user asks what a term or a claim means, drop the brief shape entirely.
Say the thing in ordinary infrastructure words, then one line on why it matters
here.

> Kubernetes asked the storage driver to mount a disk. The storage driver asked
> the node OS to run normal Linux storage commands. The node OS said it doesn't
> have that filesystem shape.
>
> Why it matters here: the fix belongs in the node image, not in the driver.

## Forward

When the brief is going to someone else, such as teammates, technical managers,
a change advisory board, or operators, three things change:

- Lead with what changes for that audience and what they have to do, in place of
  the user's decision.
- Drop internal repo, plan, and branch names. They resolve for nobody outside
  the work.
- Keep the conclusion first. The detail-then-brief order above exists because
  of how a long response sits in the user's own terminal; a forwarded reader
  isn't scrolling that window, so lead with the call instead of closing on it.

Ask who the audience is when it isn't stated.

## Tuning

When a brief misses and the user wants this skill changed, read
`references/tuning.md` first. It covers what counts as signal, how to find the
evidence, and how to edit without letting the file grow forever.
