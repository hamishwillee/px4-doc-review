# px4-doc-review

A Claude Code skill for editorial review of [PX4-Autopilot](https://github.com/PX4/PX4-Autopilot)
documentation pull requests (`docs/en/`).

It performs the following checks on every changed doc file:

- Grammar and spelling (British English, with the project's own terminology exceptions)
- Compliance with the [PX4 documentation style guide](https://docs.px4.io/main/en/contribute/docs#style-guide)
- Structural correctness: file placement, `SUMMARY.md` entries, heading structure,
  images, and cross-links
- Technical accuracy, checked against sources appropriate to the part of the docs tree
  the file is in — firmware source, sibling pages, linked vendor material, or the
  project's own contributing guide

The skill does not edit files or post comments to GitHub.
The output is a tabular report in chat, which you can assess and use for your review,
and it can write that report to a markdown file if you ask for one.

Inspired by [mdn-pr-review](https://github.com/mdn/smithy/tree/main/skills/mdn-pr-review),
adapted for a docs tree where different parts of the project need different accuracy
checks against different sources of truth.

## Contents

- [Skill structure](#skill-structure)
- [Why the lenses are split this way](#why-the-lenses-are-split-this-way)
- [Sources of truth](#sources-of-truth)
- [Installing](#installing)
- [Requirements](#requirements)
- [Using](#using)
- [Output](#output)

## Skill structure

```
px4-doc-review/
├── SKILL.md                          # the main agent's instructions: scope, dispatch, report
├── references/
│   ├── shared.md                     # read by every lens: sources of truth, routing table, severity
│   ├── style-guide.md                # the PX4 style guide, extracted as checkable rules
│   ├── grammar-style.md              # universal lens: grammar, spelling, style guide
│   ├── structural.md                 # universal lens: placement, navigation, page mechanics
│   ├── accuracy-hardware.md          # accuracy lens: airframes, boards, sensors, wiring
│   ├── accuracy-flight-behavior.md   # accuracy lens: flight modes, parameters, config
│   ├── accuracy-developer.md         # accuracy lens: middleware, modules, APIs, build/CI
│   ├── accuracy-simulation.md        # accuracy lens: sim_* pages
│   └── accuracy-contribute.md        # accuracy lens: contribute/getting-started/tutorials
├── preferences.example.md            # boilerplate to copy to ~/.config/px4-doc-review/preferences.md
└── README.md                         # this file
```

## Why the lenses are split this way

MDN's `mdn-pr-review` splits its four lenses by *kind of check* (structural, lint,
clarity, accuracy) because every page in `mdn/content` follows the same template and is
checked against the same specs and the same compat data — the axis that varies is what
sort of problem you're looking for, not what part of the site you're in.

PX4-Autopilot's docs don't have that uniformity. A hardware page (`airframes`,
`flight_controller`) is checked against wiring diagrams and vendor datasheets; a
flight-mode page (`flight_modes`, `config`) is checked against firmware parameter
source; a developer page (`middleware`, `modules`) is checked against build tooling and
APIs; a simulation page has one foot in PX4's own tooling and one in an external
simulator's docs. Those four kinds of accuracy check share almost nothing, so folding
them into one "accuracy" lens would mean every review either overloads one subagent with
several sources of truth, or checks the wrong source against the wrong page.

Grammar/spelling and style-guide compliance, on the other hand, *are* uniform across the
whole docs tree — every page is prose in the same style guide — so those two stay as
single lenses that run over every changed file, the same way MDN's lint and clarity
lenses do.

The result: two universal lenses (`grammar-style`, `structural`) plus one accuracy lens
per doc category, selected by a routing table (`references/shared.md`) keyed on the
top-level folder under `docs/en/`.

## Sources of truth

Listed in order of precedence in `references/shared.md`; a claim backed by a higher
source outranks one backed by a lower source:

1. The code change in the same PR, when the PR pairs a doc change with a source change
2. The firmware source on the PR's base branch (parameters, module metadata, uORB
   messages, driver/board source)
3. Other published doc pages on the same topic, especially vehicle-type siblings
4. The style guide (`references/style-guide.md`)
5. Manufacturer/vendor material linked from the page
6. Past review comments on the same PR or a linked issue
7. This skill, last on purpose: everything above it is published and reviewable,
   `SKILL.md` isn't

Where two genuinely contradict each other, the skill reports the contradiction instead
of picking a winner.

## Installing

Clone this repo, then symlink it into Claude Code's skills directory:

```bash
git clone https://github.com/hamishwillee/px4-doc-review.git ~/github/hamishwillee/px4-doc-review
ln -s ~/github/hamishwillee/px4-doc-review ~/.claude/skills/px4-doc-review
```

Install at user level (`~/.claude/skills/`) rather than per-project, otherwise the skill
only fires when you're working inside this repo itself. A symlink resolves to whatever
the clone has checked out, so `git pull` in the clone is the only update step.

### Reviewer preferences, optional

Copy the boilerplate `preferences.example.md` that ships with the skill into a new file
at `~/.config/px4-doc-review/preferences.md`, then edit it to your taste:

```bash
mkdir -p ~/.config/px4-doc-review
cp ~/.claude/skills/px4-doc-review/preferences.example.md ~/.config/px4-doc-review/preferences.md
```

## Requirements

- **`gh` CLI, recommended**: The skill reads the PR, its diff, and any linked issue
  through `gh` when it's installed. Without it, the skill falls back to unauthenticated
  HTTPS at 60 requests an hour.

  In Claude Code you don't have to configure permissions for this: the skill's own
  frontmatter pre-approves `gh pr view`, `gh pr diff`, `gh pr list`, and `gh api` for the
  turn that invokes it.

- **A local clone of `PX4-Autopilot`, recommended**: verifying a claim against firmware
  source (parameters, module metadata, uORB messages) is far cheaper against a local
  clone than fetching individual files over the GitHub API. The skill checks common
  locations and falls back to fetching raw file contents from GitHub when no local
  clone is found — see `references/shared.md`, "Reaching the sources".

## Using

To trigger the skill, run the following command with the PR number:

```
/px4-doc-review 24601
```

A bare number is taken as a `PX4/PX4-Autopilot` PR. The skill also triggers with natural
language, such as "review this PX4 docs PR", "doc review on PX4-Autopilot #24601", or a
pasted PR URL.

## Output

The review report has three sections:

1. **PR triage summary.** Bug count, whether a linked issue is solved, findings by area
   (Structural / Style guide / Technical accuracy), and every bug-severity finding with
   its line, so you can tell whether the PR needs you before reading any of it.
2. **Issue summary per file.** Counts per file, ordered by bug count.
3. **Detailed per-file review of changed lines.** A row per issue with line, severity,
   and suggested fix.

Severity is graded by consequence to the reader, not by size:

| Severity | Meaning |
| -------- | ------- |
| `bug` | The reader ends up misinformed, follows a step that doesn't work, or the page fails to render correctly |
| `style` | Violates a named rule, but the meaning survives |
| `minor` | No rule violated; a preference the author can decline |
