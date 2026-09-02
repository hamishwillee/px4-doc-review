---
name: px4-doc-review
description: Editorial review of PX4-Autopilot documentation pull requests (docs/en/ in the PX4/PX4-Autopilot repo). Use when the user asks for a review or doc review on a PX4-Autopilot PR touching docs/en/, or pastes a PX4-Autopilot PR URL or number. Use code-review instead when the PR changes only firmware source, build, or CI machinery with no docs/en/ changes.
allowed-tools: Bash(gh pr view:*) Bash(gh pr diff:*) Bash(gh pr list:*) Bash(gh api:*) Bash(git -C:*) Bash(grep:*)
---

# PX4 documentation PR review

You are reviewing PX4-Autopilot documentation (`docs/en/`) for editorial and technical
correctness. This is prose review, not code review: the deliverable is a report of
issues with line numbers.

**Read-only**, here and in every subagent this review spawns.
Never modify a file in the repository under review, and never run `gh pr comment`,
`gh pr review`, or any other write operation.
The one file you may create is the report itself, only when the reviewer asks for it and
only outside the clone.
Your output is the report; the reviewer posts it to GitHub themselves, after reading it.

## Workflow

Scope, Dispatch, and Report run here, in this context.
Two lenses apply to every changed file — grammar-style and structural — and one or more
**accuracy** lenses apply per file depending on which part of the docs tree it's in,
routed by the table in `references/shared.md`. Verify runs inside each lens agent,
against the requirements in `references/shared.md`.

The files in this skill directory:

- `references/shared.md`: the read-only rule, sources of truth and how to reach them,
  the category routing table, verification, and severity definitions
- `references/style-guide.md`: the PX4 documentation style guide, extracted and
  rewritten as checkable rules
- `references/grammar-style.md`, `references/structural.md`: the two universal lenses
- `references/accuracy-hardware.md`, `references/accuracy-flight-behavior.md`,
  `references/accuracy-developer.md`, `references/accuracy-simulation.md`,
  `references/accuracy-contribute.md`: one lens per doc category
- `preferences.example.md`: boilerplate for a reviewer's own preference file

Read `references/shared.md` yourself before reporting: merging findings and counting the
triage table both depend on the severity definitions and the routing table.

### 1. Scope

A bare PR number means `PX4/PX4-Autopilot`; any other repo has to be named, as
`owner/PX4-Autopilot#456` or a full URL (a PR opened from a fork is still identified by
its number on `PX4/PX4-Autopilot`, since that's the base repo the PR lives against).

Stop and say so if the PR is closed, merged, a draft, or automated (dependency bumps,
metadata regeneration, Crowdin translation sync, bot commits).

Read the PR description and mine it for what the change is and why.
When it's empty, note that in the report and review anyway.

**A linked issue is part of the scope.**
When the description links or closes an issue, read the issue and judge the PR against
what it asked for: solved, partially solved, or not addressed.
When it's partial, list what the issue asked for that the PR doesn't do.

**Filter to `docs/en/`.**
Take the PR's full changed-file list, but only `docs/en/**` files are reviewed. A file
under `docs/<other-lang>/` is flagged once by the structural lens as a bug (see
`references/structural.md`) rather than reviewed as prose. A non-doc file changed in the
same PR (`src/`, `boards/`, `msg/`, ...) is never reviewed itself, but its diff is a
sources-of-truth input — read it before dispatching, since it's the highest-precedence
evidence for whatever the docs change describes.

If no file under `docs/en/` changed, say so and stop: this skill has nothing to review.

**Reviewer preferences.**
Load `~/.config/px4-doc-review/preferences.md` if it exists and apply it here, not in
the lens agents.
Skip it silently if it's absent.

Preferences may set five things: the shape of the output (tables or lists, which
columns, what to group by, whether to include the triage summary), where to place extra
emphasis, which severities to suppress, questions to establish before reviewing, and
tone.

Preferences may not override the sources of truth or their precedence, the read-only
rule, or the requirement that every finding carry evidence.
A preference that tries to is reported as a conflict and not applied.

Every changed `docs/en/` file gets reviewed.

**Route and batch.**
For each changed `docs/en/` file, look up its category in the routing table in
`references/shared.md` by its top-level folder under `en/`. Group files by category.
Within each category, and separately for the grammar-style pass, fill batches to roughly
**1500 changed lines**, giving a file whose own diff exceeds that a batch to itself.
Structural runs unbatched over every changed `docs/en/` file, the same way grammar-style
and the accuracy lenses are batched.

When the PR needs more than one batch total, say how many and ask the reviewer how to
proceed: every batch in turn, or a subset of files they name.

### 2. Dispatch

Spawn one subagent per lens instance: one grammar-style pass per batch, one structural
pass unbatched, and one accuracy pass per category present in the diff (further split
into batches if a single category exceeds the line budget).

Give each agent the prompt below, filling the bracketed slots. Pass it as written rather
than as your own summary of it: the agent's rules come from the files it reads, and a
paraphrase drops the ones you happen not to restate.

> You are the [lens] lens of a PX4-Autopilot documentation review, and you are
> read-only: never modify a file, and never run `gh pr comment`, `gh pr review`, or any
> other write operation.
>
> Read ${CLAUDE_SKILL_DIR}/references/shared.md in full, then
> ${CLAUDE_SKILL_DIR}/references/[lens-file].md in full. Those two files are your
> instructions. Load only the sources they name, once for your whole assignment rather
> than once per file.
>
> Repository PX4/PX4-Autopilot, PR [number], head SHA [sha], base SHA [sha].
> Your assignment is [every changed docs/en/ file in the PR / this batch]: [file paths].
> [If the PR also changes non-doc files, list them here as context: these are a source
> of truth per shared.md, not files to review.]
>
> Work the diff systematically. Your lens is done only when every rule your file names
> has been applied to every changed line you hold. Eight rules out of ten is not done.
>
> Verify every finding as references/shared.md requires, then return one row per
> finding, carrying the file path, the line number, the issue, the severity, the
> suggestion, and the evidence you verified against. If you find nothing, say so rather
> than returning prose.

`${CLAUDE_SKILL_DIR}` is this skill's own directory, and Claude Code substitutes it for
you.
On a harness that leaves it unsubstituted, replace it yourself with the absolute path of
the directory this file was loaded from.
Never pass a relative path: the working directory during a review is the
`PX4-Autopilot` clone, not the skill directory, so a relative read fails and the lens
then runs with no rules at all.

**Work silently.**
Say nothing between dispatching and the report itself: no plan, no "dispatching the
lenses", no per-lens status, no "waiting on results", no count of what has come back.
The harness already shows the reviewer that tool calls are running, and the report is
the first thing you write.

**Without subagents.**
On a harness that can't spawn them, read `references/shared.md`, `references/style-guide.md`,
`references/grammar-style.md`, `references/structural.md`, and the accuracy file for
each category present, then run the lenses in sequence over the whole PR, one lens at a
time, holding to the same verification and the same return shape.
Add one line below the tables saying the review ran in a single context, because the
lenses no longer shield each other's findings from one another.

### 3. Report

Collect what the lens agents returned and merge it: two lenses can catch one problem, so
same file and same line describing the same problem is one row, keeping the more
specific evidence and the higher severity.
Two genuinely different problems on one line stay two rows.

The report has three captioned sections in this order: **PR triage summary**, **Issue
summary per file**, and **Detailed per-file review of changed lines**.
Caption each one, so a reviewer scrolling knows which is which.
Open at the triage summary, with no header block ahead of it: no PR title, author,
state, head SHA, review date, or batch count, since the reviewer already knows what they
asked you to review.

**In chat by default, in a file when asked.**
A reviewer who asks for the report as a file, or whose preferences set a path, gets the
same three sections written there and a one-line confirmation in chat.
A file is read away from this conversation, so that copy opens with a title line naming
the PR number and its own title, then three labelled lines and nothing else:

```markdown
# PX4-Autopilot PR 24601 — Fix ROMFS airframe link in VTOL config page

- PR: https://github.com/PX4/PX4-Autopilot/pull/24601
- Head SHA: fada6198026a53cd77293b8b551d06da8edfbb07
- Reviewed: 2026-09-02
```

Label all three, so none of them reads as loose text, and put nothing else above the
triage summary.
The title is what identifies the file in a directory of them.
Write it outside the clone under review, and overwrite the path rather than editing
around what's already in it.

**Report findings, not machinery.**
The reviewer reads the report to judge the PR, so nothing in it describes how the review
ran.
Never name a lens, say how many ran, or write that something was confirmed,
cross-checked, or verified by one: that a finding is in the report already means it
passed verification.
A file with no findings gets its row in the per-file summary and nothing else: no
section of its own in the detailed review, no heading, and no line saying it was checked
and came back clean.

**PR triage summary.**

It answers one question: does this PR need the reviewer's attention, and where.
Count it from the merged findings, so the totals match the tables.

Open with the bug count and where the bugs are, as "2 bugs in 1 of the 3 changed files",
so the count of files carrying a bug is never read as the count of files changed.
With no bugs at all, say "No bugs in 3 changed files": "0 bugs in 0 of 3 changed files"
reads as though only some of the files were reviewed.
Match singular and plural to the numbers, so one changed file is "1 changed file".
Follow it with the changed-line and finding totals.
When the PR links an issue, add one line for whether it solves it, and when partial,
what it leaves undone.

| Issues found | bug | style | minor | total |
| ------------ | --- | ----- | ----- | ----- |
| Structural | | | | |
| Style guide | | | | |
| Technical accuracy | | | | |

The `total` column sums each row, so the reviewer can see which area accounts for the
bulk; the three row totals add up to the finding total in the opening line.

Label the rows by area, never "Template compliance" or "Style guide compliance": the
cells count failures, so a compliance label makes `0` read as zero compliance when it
means zero issues.

Three rows, in that order, and no others: grammar/spelling findings count under Style
guide alongside the emphasis/heading/formatting rules, since both come from the
grammar-style lens; every accuracy-lens finding, regardless of category, counts under
Technical accuracy.

When there are bugs, close the section by listing every one, each with its file and
line, so the reviewer can judge the count instead of trusting it.
When there are none, close the section after the table: the opening line already said
so, and a line reading "No bug-severity findings" repeats it.

**Issue summary per file.**

| File | bug | style | minor | total |
| ---- | --- | ----- | ----- | ----- |

Ordered by bug count, so the file needing the most attention sits at the top.
Every changed `docs/en/` file gets a row, including the ones with no findings.

**Detailed per-file review of changed lines.**

One table per changed file that has findings, the file path as a heading above it, each
table sorted by line number ascending.
A file with no findings appears only in the per-file summary above, so this section
skips it entirely.

Severity is defined in `references/shared.md`, and the lens agents assign it by those
definitions; a merge keeps the higher of the two.

**Example rows**, for a flight-mode config page hunk:

| Line | Issue | Severity | Suggestion |
| ---- | ----- | -------- | ---------- |
| 24 | `MPC_LAND_SPEED` default given as `0.7 m/s`; source (`src/modules/mc_pos_control/PositionControl.cpp`) defines it as `0.5` | bug | Correct the default to `0.5 m/s` or update the source reference |
| 41 | "The vehicle will land, using the descent rate to control the approach": `using` attaches to the vehicle, which doesn't use the descent rate | style | "The vehicle lands, controlling its approach with the descent rate." |
| 58 | Section heading "Landing Behaviour" uses Title Case | style | Change to sentence case: "Landing behaviour" |

Other requirements:

- **One issue per row.** Never split a single sentence's problem across rows.
- **One row per line**, unless the extra problems on it carry different severities.
  Three rows on one line reads as piling on, so fold same-severity problems into one row
  that names each of them, and keep a `bug` in a row of its own.
- Write each row so it's clear to the **author**, not just to you.
  If a row is hard to parse, rewrite it.
- **Keep the Suggestion column to one line.** Name the fix there, in a phrase or a short
  sentence.
  A rewrite longer than that goes below the tables as a suggestion block, keyed to its
  file and line, because a wide column is what makes a report hard to read.

**What goes below the tables.**
No closing remarks; the summary opens the review and the tables carry it.
These seven are permitted, in this order, each only when it applies:

1. Suggestion blocks too long for the Suggestion column, each keyed to its file and line
2. Pre-existing issues on lines this PR didn't change (the Verify section of
   `references/shared.md`)
3. Where the sources disagree (the Sources of truth section of `references/shared.md`)
4. A cross-vehicle-type or cross-simulator inconsistency noted by an accuracy lens as a
   question for the reviewer, rather than a finding on the changed lines themselves
5. A single note if a changed folder wasn't in the routing table (the Category routing
   section of `references/shared.md`)
6. A single note if the PR description was empty (the Scope step)
7. A single note if the review ran in one context rather than one subagent per lens
   (the Dispatch step)

To cite a line as a GitHub link, use the head SHA in full, not abbreviated, and a range
with one line of context on each side:
`https://github.com/PX4/PX4-Autopilot/blob/<full-sha>/docs/en/.../index.md#L12-L16`
