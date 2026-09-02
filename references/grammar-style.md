# Grammar-style lens

Applies to **every** changed `docs/en/` file, regardless of category.
This is the one check every part of the PX4 documentation shares, so it runs uniformly and doesn't consult the routing table.

Your rules are `references/style-guide.md` in full, plus the grammar/spelling checks below it isn't limited to.
Both are `style` findings unless noted otherwise; run through each changed line once for grammar/spelling, once for the style guide table, rather than skimming for both at once.

## Grammar and spelling

- Spelling: British English throughout, minus the industry-terminology exceptions in `style-guide.md`.
  Flag genuine misspellings and Americanisms outside the exception list; don't flag a proper noun, product name, or anything inside a code span, parameter name, or file path.
- Grammar: subject-verb agreement, verb tense consistency within a procedure (a numbered-step list shouldn't drift between imperative and descriptive mood mid-list), pronoun-antecedent agreement, correct article use, comma splices, dangling or misplaced modifiers.
- Sentence-level clarity: a sentence that's grammatically valid but genuinely hard to parse (a long noun cluster, an ambiguous "it"/"this" with no clear antecedent, a modifier that could attach to either of two things) — flag it with the ambiguity named and a rephrase.
- Typos and duplicated words ("the the", "a a"), including inside headings and list items, not just paragraph prose.
- Terminology drift: the same concept named two different ways in nearby text (e.g. "flight controller" and "autopilot board" used interchangeably for the same thing on one page) without the page ever establishing they're synonyms.
  Flag once per page with both instances, not once per occurrence.

## Style guide compliance

Work `references/style-guide.md` section by section against the diff:

- Spelling and grammar → already covered above, don't double-report.
- Emphasis markup table: bold/italic/code used for the wrong purpose.
- Headings: wrong level, wrong capitalisation, styled heading text.
- Admonitions: an altered `::: tip`/`::: info`/`::: warning` keyword — this one is `bug`, not `style`, because it breaks rendering.
- Line breaks: a paragraph hard-wrapped to a fixed column instead of broken on sentences.
- Videos: a YouTube embed not in `<lite-youtube>` form; a new long tutorial video where a shorter written procedure would serve (flag as `minor`, not `style` — this is a preference, not a hard rule).
- File/folder placement and naming, and image sizing/format/alt-text conventions — only when the diff actually adds or renames a file or image; don't re-litigate an unchanged file's name.

## What this lens doesn't cover

Leave to the structural lens: heading *order* within a page template, SUMMARY.md entries, and whether a page belongs in its folder at all.
Leave to the accuracy lenses: whether a claim is technically true.
This lens only checks how something is said, never whether it's correct.

Also leave alone, because tooling already catches it and re-reporting it is noise: Prettier-fixable whitespace/indentation down to the character, markdownlint-style list marker consistency, and trailing newline issues.
Flag formatting only when it's visibly wrong to a reader, not merely non-canonical.
