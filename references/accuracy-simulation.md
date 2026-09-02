# Accuracy lens: simulation

Covers the **simulation** category from the routing table in `shared.md`: the simulator-specific setup pages (`sim_gazebo_gz`, `sim_gazebo_classic`, `sim_jmavsim`, `sim_airsim`, `sim_flightgear`, `sim_jsbsim`, `sim_rotorpy`, `sim_sih`, `sim_xplane`, `sim_hawkeye`) and the shared `simulation` overview page.

These pages straddle PX4's own build/launch tooling and each external simulator's own tooling.
Treat the PX4 side with the same rigour as the developer lens; treat claims about the external simulator's own behaviour more cautiously, since this skill doesn't have that simulator's source to check against.

## PX4-side launch commands

- A `make px4_sitl <target>` invocation, launch script path, or CMake sim target name: checked against `Tools/simulation/` and the relevant `make` targets on the base branch.
  A target that no longer exists or was renamed is `bug`.
- A claimed default (spawn location, default vehicle model, default world) checked against the launch script or model files it comes from when readily checkable; when it isn't, don't invent a `bug`.

## External simulator claims

- A claim about the external simulator's own UI, install steps, or behaviour (Gazebo's own CLI flags, AirSim's own settings file format, etc.): only verified when the page itself links the simulator's own docs — `WebFetch` that link rather than trusting memory of the tool, since these change version to version and this skill has no local source for them.
- A version compatibility claim ("requires Gazebo Harmonic or later") checked against the linked simulator docs or a nearby PX4 support-matrix page (e.g. the `simulation` overview) if one exists in the diff or repo; otherwise note it as unverifiable rather than flagging it as wrong.

## Cross-simulator consistency

Several `sim_*` pages document the same PX4-side concepts (SITL, model/world file locations, MAVLink connection defaults) independently per simulator.
A PX4-side claim that contradicts the equivalent claim on another `sim_*` page is worth flagging even when neither is independently confirmed wrong.

## What this lens doesn't cover

Flight-mode and parameter behaviour exercised inside a simulated flight belongs to the flight-behavior lens.
Companion-computer/ROS integration used alongside a simulator belongs to the developer lens.
