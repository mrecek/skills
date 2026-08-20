# Tuning this skill

Read this when a brief missed and the user wants the skill changed. Nothing here
runs on its own.

## What counts as signal

A miss is one of these, and the user usually says so in the moment:

- A noun in the brief didn't resolve for them. "What the fuck is X", "what do you
  mean by X", "what is this thing you're referencing".
- The brief carried weight that moved no decision. "Too much detail", "don't talk
  about the A01 A02 gates", "I don't need all that".
- The brief went shallow when they wanted depth, or hedged instead of committing
  to a call.
- A brief they forwarded didn't land with its audience.

One occurrence is an anecdote. Wait for a second before it becomes a rule.

## Finding the evidence

The pair is what makes it evidence: **what the brief said, and what the user said
back.** Either one alone is guesswork.

In the moment, both are in the conversation. For a wider look, agent harnesses
keep session transcripts on disk. Search the user's own messages for the phrases
above, then read the assistant turn immediately before each hit. That pairing
built the current rules, and it's repeatable whenever the skill needs
re-grounding.

## Changing SKILL.md

**Sharpen before adding.** Most misses are an existing rule that was too vague,
not a rule that was missing. Check whether the altitude list or the completion
criterion already covers the case and simply didn't bite. Sharpening is cheaper
than growing.

**Pair every addition with a cut.** Skills grow forever because adding feels safe
and removing feels risky. If a new rule earns its place, find the line it makes
redundant and delete that line.

**Trace every line to a real correction.** If you can't name the brief that
produced a rule, it's padding. Cut it.

**Re-read the exemplar after editing.** It's the ground truth for register. A
change that would have made that brief worse is the wrong change, however
reasonable the correction that prompted it.

## The failure to watch for

Corrections cluster around "too much" and "I don't understand." Left unchecked
they accumulate into "be brief" and "be simple," which is a different skill and a
worse one. The user is a Sr. Systems Architect and handles hard architecture
fluently. Every complaint in the record has been about references he couldn't
resolve, never about difficulty.

Altitude governs which nouns appear, not how hard the ideas are allowed to be.
Protect that line above any individual correction.
