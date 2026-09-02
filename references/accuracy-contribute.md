# Accuracy lens: contribute/meta

Covers the **contribute** category from the routing table in `shared.md`: the
contributor guide itself, getting-started, tutorials, release notes, test cards, and the
top-level `index.md`.

These pages describe the project's own processes rather than the firmware, so the
sources of truth are the repo's own meta-files rather than `src/`.

## Process claims

- A described contribution workflow (branch naming, PR labels, required checks, review
  process) checked against `CONTRIBUTING.md`, `MAINTAINERS.md`, and this same page's own
  other sections for internal consistency. A step that contradicts `CONTRIBUTING.md` is
  `bug`.
- A referenced GitHub label, template, or workflow file: checked it currently exists
  (`.github/` on the base branch, or the label list via `gh api` if checkable). A
  reference to a removed label/template is `bug`.

## Getting-started and tutorial steps

- An installation or setup command: checked the same way the developer lens checks CLI
  samples — does it still exist, does it still take that form. A step that no longer
  works for a new contributor is `bug`, since this is the page most likely to be
  followed literally by someone with no other context to fall back on.
- A referenced tool version (Node, Python, a specific package) checked against any
  version pin the repo states elsewhere (`package.json` engines field,
  `Tools/setup/requirements.txt`, or similar) when readily checkable.

## Release and version claims

- A version number, release date, or "as of version X" claim checked against
  `docs/en/releases/` or the repository's actual tags/releases when checkable
  (`gh release list` or similar); otherwise note as unverifiable.

## What this lens doesn't cover

Firmware behaviour and hardware claims occasionally referenced from a tutorial belong to
the flight-behavior or hardware lens respectively if you're reviewing a straddling file
yourself; this lens only owns the process/meta claims.
