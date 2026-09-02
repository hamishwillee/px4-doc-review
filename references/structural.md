# Structural lens

Applies to **every** changed `docs/en/` file, and to the PR as a whole rather than
line-by-line — it runs unbatched over the full set of changed doc files, the same way
the grammar-style lens runs over every file but per-line.

Your job is placement, navigation, and page mechanics: whether the change is *findable*
and *renders*, not whether its prose or its facts are right.

## Translated-file guard

If any changed file is under `docs/<lang>/` for a `<lang>` other than `en` (`docs/ko/`,
`docs/zh/`, `docs/uk/`, ...), that's a `bug`: translations are Crowdin-managed and
shouldn't be hand-edited in a PR against `main`. Name every such file; don't review the
translation quality itself.

## New pages

- A new `.md` file under `docs/en/` needs a corresponding entry in `docs/en/SUMMARY.md`.
  Check the PR's file list for both; a new page with no `SUMMARY.md` change is a `bug`
  (unreachable via navigation), not a `style` finding.
- A new page sits in an existing, appropriately-topical sub-folder of `docs/en/` with no
  further nesting under it — a new file placed directly under `docs/en/` (outside any
  topic folder) or nested two levels deep is a `style` finding per the style guide's
  file-placement rule.
- A genuinely new topic area with no existing folder to hold it is worth a note rather
  than a rule violation — flag it as a question for the reviewer, not a `bug`.

## Heading structure

- Exactly one `#` (the page title), matching or closely paraphrasing the `SUMMARY.md`
  entry text for that page — a page title that no longer matches its sidebar entry is
  `style`.
- No heading level skips from where the diff last touched (`##` straight to `####`).
- A moved or renamed heading that other pages link to by anchor: check whether any
  changed or nearby page links `#old-anchor-text`; a dangling in-repo anchor link is a
  `bug`.

## Images and assets

- A newly referenced image path resolves (`../../assets/...` two levels up from the
  page's folder into `/assets/`) and the target file exists in the diff or already in
  the repo. A reference to an image that isn't in either is `bug` — the page will show a
  broken image.
- New image files land under a sub-folder of `/assets/`, not loose in `docs/en/`.

## Cross-links

- An in-repo relative link (`../../hardware/...`, `./index.md`, etc.) added or changed
  by the PR resolves to a real file at its head SHA. A broken relative link is `bug`.
- An external link added by the PR: spot-check it resolves (`WebFetch`) rather than
  assuming; a link that 404s is `bug`.

## What this lens doesn't cover

Leave prose quality, spelling, and style-guide emphasis/heading-capitalisation rules to
the grammar-style lens. Leave whether a claim is factually correct to the relevant
accuracy lens. This lens only checks that the page is structurally sound and reachable.
