# Progress

*This file tracks the project's overall status, including what's working, what remains to be built, known issues, and the evolution of project decisions over time.*

## Current Status

- **Phase:** Initial Code Analysis & Documentation Complete.
- **Memory Bank:** Updated based on analysis of all core JBI files and cross-referenced with INFORM III manuals.
- **Code Analysis:** All core JBI files analyzed. High-level understanding achieved and refined based on manual review.

## What Works

- The basic file structure for the robot program exists.
- Initial Memory Bank documentation framework is in place.
- The master job (`MASTER-START-ML.JBI`) calls sub-jobs sequentially with arguments.
- Both welding jobs (`BOVEN`/`ONDER`) use arguments and separate global counters to manage and execute multiple weld passes.
- They call `MACRO1 MJ#(1)` (`SHIFT-R1.JBI`) to apply pass-specific 6D offsets.
- They call `MACRO1 MJ#(2)` (`WEAVING.JBI`) to set pass-specific weave parameters into `D080-D082`.
- They call `MACRO1 MJ#(0)` (`ESAB1JOB.JBI`) to select ESAB weld schedule (10/11) and check errors.
- `BOVEN` welds between `P001`/`P002`.
- `ONDER` constructs `P003`/`P004` using XYZ from `P001`/`P002`, a Z-offset, and local orientations.
- Transition jobs (`VAN-*.JBI`) use fast `MOVJ` commands through predefined `PULSE` points.
- `TESTUF-ARNE.JBI` defines `UF#(15)` using the 3-point method based on operator-taught points (Origin, XX-axis) captured via `GETPOS`, and sets global `P001` (Weld Start) and `P002` (Weld End) from these points.
*(Detailed pass parameters and overall welding result still require review/simulation).*

## What's Left to Build

- **Code Analysis:** All core JBI files have been analyzed at a high level and cross-referenced with manuals.
- **Understanding Core Logic:**
    - Review the specific arguments passed to `MACRO1 MJ#(1)` (shift) and `MACRO1 MJ#(2)` (weave) in each `*n` block within the welding jobs to understand the exact multi-layer fill strategy.
    - Confirm how base welding parameters are defined within ESAB schedules 10 and 11. (Requires ESAB interface/knowledge).
    - Note: `DOUT OT#(46) ON` activates cooling system; no corresponding OFF command found.
- **Verification:** Confirm the interactions between jobs and macros.
- **Potential Refinements/Improvements:** Identify areas for optimization, error handling improvements, or clarification once the code is understood.
- **Testing/Simulation:** (Outside current scope but eventual goal) Verify program functionality.

## Known Issues

- None identified yet, as code analysis has not begun.

## Evolution of Decisions

- **[Timestamp: 2025-05-04 ~12:57 PM]** Initialized Memory Bank.
- **[Timestamp: 2025-05-04 ~12:59 PM]** Analyzed `MASTER-START-ML.JBI`.
- **[Timestamp: 2025-05-04 ~1:00 PM]** Analyzed `MULTILAYER-TESTAFLOOP-BOVEN.JBI`.
- **[Timestamp: 2025-05-04 ~1:03 PM]** Analyzed `MULTILAYER-TESTAFLOOP-ONDER.JBI`.
- **[Timestamp: 2025-05-04 ~1:04 PM]** Analyzed `SHIFT-R1.JBI` (MJ#(1) - Shift).
- **[Timestamp: 2025-05-04 ~1:05 PM]** Analyzed `WEAVING.JBI` (MJ#(2) - Weave).
- **[Timestamp: 2025-05-04 ~1:07 PM]** Analyzed `ESAB1JOB.JBI` (MJ#(0) - ESAB Control).
- **[Timestamp: 2025-05-04 ~1:08 PM]** Analyzed `VAN-BOVEN-NAAR-ONDER.JBI` (Transition Down).
- **[Timestamp: 2025-05-04 ~1:09 PM]** Analyzed `VAN-ONDER-NAAR-BOVEN.JBI` (Transition Up).
- **[Timestamp: 2025-05-04 ~1:11 PM]** Analyzed `TESTUF-ARNE.JBI` (User Frame setup, P001/P002 definition).
- **[Timestamp: 2025-05-04 ~1:15 PM]** Read INFORM III manuals (Parts 1-6).
- **[Timestamp: 2025-05-04 ~1:19 PM]** Clarified `DOUT OT#(46) ON` controls cooling system (per user).
