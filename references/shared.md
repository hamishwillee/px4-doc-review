# Shared rules

Read by every lens, before that lens's own file.
This holds what all lenses share: the read-only rule, the sources of truth and their precedence, how to reach each source, the category routing table, verification, and severity.

## Read-only

You are read-only.
Never modify a file in the repository under review, and never run `gh pr comment`, `gh pr review`, `gh pr edit`, or any other write operation.
The one file you may create is the report itself, and only the main agent does that, only when the reviewer asks for it, and only outside the clone.

## Scope reminder

PX4 documentation lives at `docs/en/` inside the `PX4/PX4-Autopilot` monorepo, alongside the firmware source.
Only files under `docs/en/` are reviewed.
A PR that also touches `src/`, `boards/`, `msg/`, or similar is still in scope for its `docs/en/` files, and the non-doc changes in that same PR are a **source of truth** (see below), not something to review in their own right — never raise a finding on a `.cpp`, `.yaml`, or other non-doc file.

Files under `docs/<lang>/` for any `<lang>` other than `en` (`ko`, `zh`, `uk`, ...) are Crowdin-managed translations.
They should never be hand-edited in a PR against `main`.
A PR that touches one is a `bug`-severity structural finding, not a translation-quality review — don't assess the translation itself.

## Sources of truth

Listed in precedence order: a claim backed by a higher source outranks one backed by a lower source.
Where two genuinely contradict each other, report the contradiction instead of picking a winner.

1. **The code change in this same PR.** When a PR pairs a doc change with a source change (a new parameter, a renamed topic, a new module flag), the PR's own diff is the most current statement of truth — check the doc against it before anything else.
2. **The firmware source on the PR's base branch**, in the same `PX4-Autopilot` checkout: parameter definitions (`PARAM_DEFINE_*` in `src/**`), module metadata (`module.yaml`, `px4_module.yaml`, the `PRINT_MODULE_*` description macros), uORB message definitions (`msg/*.msg`), board/driver source.
   This is what the docs describe, so a claim that doesn't match current source is a `bug`.
3. **Other published doc pages on the same topic**, especially the vehicle-type siblings of a changed page (`flight_modes` / `flight_modes_fw` / `flight_modes_mc` / `flight_modes_rover` / `flight_modes_vtol`, and the equivalent `config_*`, `complete_vehicles_*`, `features_*` families).
   A claim that contradicts a sibling page without an evident reason is worth flagging even when neither page is independently wrong.
4. **The style guide** (`references/style-guide.md` in this skill, extracted from `docs/en/contribute/docs.md`).
5. **Manufacturer/vendor material** linked from the page under review (a flight controller or sensor datasheet, a vendor's own docs) — only for claims the page itself sources there; don't go hunting for a vendor spec the page never cites.
6. **Past review comments** on the same PR or a linked issue.
7. **This skill**, last on purpose: everything above it is published and reviewable, `SKILL.md` and these references aren't.

### Reaching the sources

- **PR diff and description, linked issue**: `gh pr view`, `gh pr diff`, `gh api`.
- **Firmware source**: prefer a local clone of `PX4-Autopilot` if the reviewer has one (check `~/github/PX4/PX4-Autopilot` and sibling common locations first, otherwise ask the reviewer or fall back to fetching raw file contents from GitHub at the PR's base SHA via `gh api repos/PX4/PX4-Autopilot/contents/<path>?ref=<base-sha>` or `raw.githubusercontent.com`).
  Never assume a clone's checked-out branch matches the PR's base — check out or fetch the base SHA rather than trusting whatever the clone currently has checked out, since a stale local branch produces false negatives.
- **Vendor/manufacturer material**: `WebFetch` the URL the page itself links.
- Grammar/spelling and the style guide need no external source beyond `references/style-guide.md` — they aren't verified against the firmware source.

## Category routing

Every changed `docs/en/` file routes to exactly one accuracy category by its top-level folder under `en/`.
This determines which `accuracy-*.md` lens reviews it; grammar-style and structural apply to every file regardless of category.

| Category | Top-level folders under `docs/en/` |
| --- | --- |
| **hardware** | `airframes`, `assembly`, `camera`, `can`, `complete_vehicles`, `complete_vehicles_fw`, `complete_vehicles_mc`, `complete_vehicles_rover`, `complete_vehicles_vtol`, `dev_kits`, `dronecan`, `esc`, `flight_controller`, `frames_airship`, `frames_autogyro`, `frames_balloon`, `frames_helicopter`, `frames_multicopter`, `frames_plane`, `frames_rover`, `frames_sub`, `frames_vtol`, `gps_compass`, `hardware`, `payloads`, `peripherals`, `power_module`, `power_systems`, `sensor`, `sensor_bus`, `smart_batteries`, `telemetry`, `uart`, `uavcan`, `vtx` |
| **flight-behavior** | `actuators`, `advanced`, `advanced_config`, `advanced_features`, `concept`, `config`, `config_fw`, `config_heli`, `config_mc`, `config_rover`, `config_vtol`, `features_fw`, `features_mc`, `flight_modes`, `flight_modes_fw`, `flight_modes_mc`, `flight_modes_rover`, `flight_modes_vtol`, `flight_stack`, `flying` |
| **developer** | `companion_computer`, `computer_vision`, `data_links`, `debug`, `dev_airframes`, `dev_log`, `dev_setup`, `development`, `log`, `mavlink`, `middleware`, `modules`, `msg_docs`, `neural_networks`, `robotics`, `ros`, `ros2`, `software_update`, `test_and_ci`, `uorb` |
| **simulation** | `simulation`, `sim_airsim`, `sim_flightgear`, `sim_gazebo_classic`, `sim_gazebo_gz`, `sim_hawkeye`, `sim_jmavsim`, `sim_jsbsim`, `sim_pterosim`, `sim_rotorpy`, `sim_sih`, `sim_xplane` |
| **contribute** | `contribute`, `getting_started`, `releases`, `test_cards`, `tutorials`, top-level `index.md` |

A folder not listed here is new since this table was written: read its `index.md` or a couple of its pages, place it in the closest-fitting category above by the same logic as the existing rows, and note the gap below the report's tables so the reviewer can add it to this table permanently.

A file whose subject genuinely straddles two categories (e.g. a `companion_computer` page that's half wiring, half MAVLink/offboard code) goes to the category the routing table names.
If the lens agent finds the *other* category's concerns unavoidable on a given file, it should still raise them rather than skip them, just under its own category's severity judgement rather than deferring the file to a second lens.
The file is still reviewed by exactly one accuracy lens, never two.

## Verification

Every finding needs evidence you actually checked, not evidence you expect to be true:

- **Grammar/style/structural**: quote the exact rule from `references/style-guide.md` (or the general grammar issue) and the offending text.
- **Accuracy**: name the source you checked it against (file path and line, or URL) and what it says, not just that something "seems wrong".
- **Line numbers**: match the head SHA's file, not a cached or paraphrased version.
  If you can't find the line by exact string match against the file as fetched, drop the finding rather than guess its location.

A finding that fails verification is dropped, not downgraded.

## Severity

| Severity | Meaning |
| --- | --- |
| `bug` | The reader ends up misinformed, follows a step that doesn't work, or the page fails to render correctly |
| `style` | Violates a named rule (style guide, or an established convention on sibling pages) but the meaning survives |
| `minor` | No rule violated; a preference the author can decline |

The line between `bug` and `style` sits at what the page tells the reader or asks them to do.
A wrong parameter default, a wiring diagram that doesn't match the connector, or a command that no longer exists is `bug`.
A heading in Title Case instead of sentence case, or American spelling outside the exceptions list, is `style`.
