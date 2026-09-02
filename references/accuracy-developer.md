# Accuracy lens: developer

Covers the **developer** category from the routing table in `shared.md`: build/dev setup, module and middleware docs, uORB, MAVLink, companion computer integration, ROS/ROS2, computer vision, debugging/logging, and test/CI docs.

These pages describe APIs, build tooling, and source-level architecture for people extending or building PX4, not end users.
The firmware source and build scripts in the same repo are the ground truth — sources-of-truth items 1 and 2 in `shared.md` apply here more directly than anywhere except flight-behavior.

## Code and CLI samples

- A shell command, build target, or CLI invocation: checked that it still exists and takes that form — grep `Tools/`, `CMakeLists.txt`, or the relevant `px4-*`/`make` target definitions on the base branch rather than trusting the sample.
  An invocation that no longer exists or was renamed is `bug`.
- A code snippet (C++, Python, uORB usage, MAVLink message construction) checked against the current API it's demonstrating: does the function/class/message still exist with that name and signature.
  A snippet using a removed or renamed API is `bug`.
- A referenced source file path (e.g. "see `src/modules/mc_pos_control/`") checked that the path exists on the base branch.
  A stale path is `bug`.

## Module and message documentation

- A module's documented purpose/behaviour, when the page quotes or paraphrases the module's own `PRINT_MODULE_DESCRIPTION`/`PRINT_MODULE_USAGE_*` macros: checked against the current macro text in source.
  Drift between the two is `bug` if it would mislead a developer about what the module does or how to invoke it.
- A uORB topic or field name mentioned in prose: checked against the `.msg` file in `msg/`.
  A field that was renamed or removed is `bug`.

## Version and compatibility claims

- A claim tied to a specific PX4 release, ROS version, or toolchain version: check it's not contradicted by more recent same-topic pages or by the file's own front matter (when the docs use version-scoped content).
  Don't verify against external release notes unless the page itself links them.

## What this lens doesn't cover

Flight-mode/parameter behaviour belongs to the flight-behavior lens even when it's demonstrated via a uORB topic or CLI command that this lens would otherwise check the existence of — check that the command/topic *exists*, leave whether the *behaviour it produces* is correctly described to the flight-behavior lens.
Hardware wiring and specs belong to the hardware lens.
Simulator-specific commands and config belong to the simulation lens even though they're also "developer" in flavour.
