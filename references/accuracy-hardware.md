# Accuracy lens: hardware

Covers the **hardware** category from the routing table in `shared.md`: airframes,
flight controllers, dev kits, frames, sensors, buses (CAN/DroneCAN/UART), power system
components, GPS/compass, telemetry radios, cameras, payloads, ESCs, VTX, and complete
vehicle write-ups.

These pages describe physical things: what connects to what, what a value means on real
hardware, what a part is called and where to get it. Get the sources of truth in
`shared.md` open before starting — items 2 (firmware source, for board/driver-defined
values), 3 (sibling pages, for consistency across frame/vehicle variants), and 5 (linked
vendor material) all apply here more than to any other category.

## Wiring and connectors

- A named connector, pin, or wire colour claim (e.g. "connect the red wire to the +
  pin", "use the `I2C2` port") checked against the board's own docs page or a linked
  pinout/datasheet. A wiring instruction that would misconnect hardware if followed is
  `bug` — this is the category where a wrong claim has physical consequences.
- A pinout table or diagram added or changed: check pin count and labelling against the
  board's other documented pinouts (a board with an existing pinout table elsewhere in
  the repo) rather than trusting the new table in isolation.

## Physical specifications

- Voltage, current, weight, dimension, and similar numeric physical specs: checked
  against a linked datasheet/vendor page when the page cites one. When the page states a
  spec with no citation and you have no way to verify it, don't invent a `bug` — note it
  under "pre-existing issues" or flag as needing a source, not as wrong.
- A changed spec value: compare against the same spec elsewhere in the docs for the same
  part (a flight controller's specs often appear on both its own page and a
  `complete_vehicles_*` page referencing it) — a mismatch between the two is worth
  flagging even without an external source.

## Part identity and naming

- Part numbers, model names, and manufacturer names spelled and formatted consistently
  with how the same part is named elsewhere in the docs (check `hardware`,
  `flight_controller`, or the relevant `frames_*`/`complete_vehicles_*` folder for prior
  mentions).
- A product marked (Discontinued), (Discouraged for New Designs), or similar in one
  place but not a changed reference to it elsewhere — flag the inconsistency.

## Vendor links

- A newly added or changed link to a manufacturer's product page, datasheet, or store
  listing: spot-check it resolves. A link that 404s or redirects to an unrelated product
  page is `bug`.

## Cross-vehicle-type consistency

Hardware described identically across `complete_vehicles_fw` / `_mc` / `_rover` / `_vtol`
should stay identical where the underlying part is the same; a claim that diverges
without an evident reason (a genuinely different config for that vehicle type) is worth
flagging even when neither instance is independently wrong — note it as a
cross-reference question for the reviewer.

## What this lens doesn't cover

Firmware parameter names/defaults, flight-mode behaviour, and configuration-software
claims belong to the flight-behavior lens even on a hardware-category page that mentions
a parameter in passing — flag it there under that lens's category if you're reviewing a
straddling file yourself (see `shared.md`'s note on straddling files), but don't attempt
to verify a parameter default against source from this lens.
