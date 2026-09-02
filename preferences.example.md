# Reviewer preferences

Copy this file to `~/.config/px4-doc-review/preferences.md` and edit it there.
The skill loads only that exact path, so a copy that keeps `.example` in its name is
never read.

The `px4-doc-review` skill loads it if it exists and skips it silently if it doesn't, so
an empty or absent file is a valid setup: you get the skill's defaults.

It lives outside the skill directory on purpose, so sharing the skill can't carry your
preferences to anyone else.

Delete any section you don't want to change.

## What you can set

**Output shape.** Tables or lists, which columns, what to group by, and whether to
include the triage summary at all. The default is a triage summary, a per-file issue
summary, and a table per changed file.

**Where the report goes.** Chat by default, or a path to write it to instead, such as
`~/reviews/PX4-Autopilot-<pr>.md`.

**Emphasis.** Checks to weight more heavily than the defaults, such as always
cross-checking a changed parameter default against source even when the claim looks
unremarkable, or always flagging a hardware page with an unsourced physical spec.

**Suppression.** Severities to drop. `minor` is the usual candidate.

**Orientation.** Questions to answer before reviewing, for an area you don't know well.
The answers select which criteria apply, so this runs before any file is read.

**Tone.** Collegial, blunt, how much hedging, whether to soften a `bug`.

## What you can't set

Preferences cannot override the sources of truth or their precedence, the read-only
rule, or the requirement that every finding carry evidence. A preference that tries to
is reported as a conflict and not applied.

---

## Output

<!-- e.g. Group findings by severity rather than by file. Drop the Suggestion column.
     e.g. Write the report to ~/reviews/PX4-Autopilot-<pr>.md as well as showing it in chat. -->

## Emphasis

<!-- e.g. Always cross-check a changed parameter default against src/**. -->

## Suppression

<!-- e.g. Don't report `minor` findings. -->

## Orient before reviewing

<!-- e.g. For any hardware-category PR, establish first:
     - Which board/part is this page about, and does it have an existing page elsewhere?
     - Is the PR adding a new part or correcting an existing one?
     - Does the PR cite a datasheet or vendor source for its claims?
     Mine the PR description for these answers before looking anywhere else. -->

## Tone

<!-- e.g. Collegial, not apologetic. No hedging. Same standard for everyone. -->
