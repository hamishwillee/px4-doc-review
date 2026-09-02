# PX4 documentation style guide (extracted)

Rewritten from the "Style Guide" section of
[Contributing to Documentation](https://docs.px4.io/main/en/contribute/docs#style-guide)
as checkable rules rather than prose, for the grammar-style lens to apply mechanically.
The published page is still the source of truth; re-read it if a rule here looks stale
(`docs/en/contribute/docs.md`, `## Style Guide`).

Everything below is a `style` finding unless noted otherwise: the meaning survives a
violation, but a named rule is broken.

## Spelling and grammar

- British English (UK) spelling and grammar throughout: "colour" not "color",
  "behaviour" not "behavior", "-ise"/"-yse" not "-ize"/"-yze", "travelled" not "traveled".
- Exception: industry-standard software terminology keeps its normal (often American)
  form even when a British variant exists — `dialog`, `program`, `disk`, and similar
  established terms are not misspellings. Don't flag these.
- Exception: a proper noun, product name, code identifier, file path, CLI flag, or
  quoted string keeps its actual spelling regardless of variant (e.g. a parameter named
  `MPC_COLOR_LED` stays as written).

## Emphasis markup

Use style markup consistently and as little as possible. Each of the three has exactly
one job; using one for another's job is a `style` finding.

| Markup | Use for | Not for |
| --- | --- | --- |
| **Bold** | Button presses and menu items the reader clicks (e.g. **Save**, **Advanced Settings**) | Tool names, file paths, emphasis for its own sake |
| _Emphasis_ (italics) | Tool/product names (e.g. _QGroundControl_, _prettier_) | Buttons, code, casual emphasis |
| `Code` | File paths, code, parameter names that aren't linked, CLI tool invocations | Tool/product names, button labels |

## Headings

- Page title is a single first-level heading (`#`); every other heading is `##` or
  deeper — never a second `#` on the page, and never skip straight from `#` to `####`.
- Headings and the page title use "First Letter Capitalisation" — capitalise the first
  letter of the heading, not every word (sentence case, not Title Case). Check
  consistency against sibling headings on the same page and comparable pages in the same
  folder when the rule alone is ambiguous.
- No bold, italics, or code markup inside a heading.

## Admonitions

- `::: tip`, `::: info`, `::: warning` (and closing `:::`) must stay exactly as written.
  This is Vitepress syntax, not translatable prose — changing the keyword breaks
  rendering. A changed admonition keyword is a `bug`, not a `style` finding.

## Line breaks

- Don't hard-wrap lines at a fixed column width. Break lines on sentence or paragraph
  boundaries instead (typically one sentence per line). A paragraph reflowed to a fixed
  width is a `style` finding — flag it as a Prettier/formatting issue, since it also
  breaks the diff for the next editor.

## Formatting

- The page should be Prettier-formatted. Don't hand-simulate a full Prettier run; flag
  only formatting that's visibly off (inconsistent list markers, spacing, trailing
  whitespace) as a `minor` pointer to run Prettier, rather than enumerating every
  micro-deviation.

## Videos

- YouTube embeds use `<lite-youtube videoid="<id>" title="<title>"/>`, not a raw
  `<iframe>` or a bare link.
- Instructional/tutorial videos should be used sparingly — they date quickly and are
  costly to maintain. Flag a newly added long-form tutorial video as `minor` with a note
  suggesting text/image instructions instead, unless it's flight footage (airframe demos
  are explicitly welcome regardless of length).

## File and folder placement

- New markdown files go in an existing, appropriately-named sub-folder of `/en/` (e.g.
  `/en/contribute/`) — no further nesting under that.
- New image files go in a sub-folder of `/assets/`; deeper nesting there is fine and
  expected.
- Folder and file names are descriptive, lower-case, words separated by underscores
  (`_`), never `image1.png`-style placeholders.

## Images

- Use the smallest size and lowest resolution that still serves the reader — this is a
  bandwidth cost for users, not a cosmetic choice, so a needlessly large embedded image
  is a `style` finding, not `minor`.
- SVG for diagrams, PNG over JPG for screenshots.
- Reference images two directories up into `/assets/`:
  `![Image description](../../assets/path_to_file/filename.jpg)`. The alt text
  describes the image content, not "screenshot" or "image".
