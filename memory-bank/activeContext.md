# Active Context

*This file tracks the current focus, recent activities, and immediate next steps. It also documents important patterns, preferences, and learnings discovered during development.*

## Current Focus

- Analyzing the core JBI files to understand the detailed program logic.
- Updating the Memory Bank with insights gained from code analysis.

## Recent Changes

- Completed initial population of all core Memory Bank files.
- Read and analyzed `MASTER-START-ML.JBI`.
- Read and analyzed `MULTILAYER-TESTAFLOOP-BOVEN.JBI`.
- Read and analyzed `MULTILAYER-TESTAFLOOP-ONDER.JBI`.
- Read and analyzed `SHIFT-R1.JBI` (Macro MJ#(1)).
- Read and analyzed `WEAVING.JBI` (Macro MJ#(2)).
- Read and analyzed `ESAB1JOB.JBI` (Macro MJ#(0)).
- Read and analyzed `VAN-BOVEN-NAAR-ONDER.JBI`.
- Read and analyzed `VAN-ONDER-NAAR-BOVEN.JBI`.
- Read and analyzed `TESTUF-ARNE.JBI`.

## Next Steps

- Update `systemPatterns.md` and `progress.md` based on the analysis of `TESTUF-ARNE.JBI`.
- Consolidate understanding and potentially identify areas for clarification or improvement.

## Active Decisions & Considerations

- Prioritized initializing the Memory Bank before deep code analysis, as per user request.
- Need to determine the most effective order for analyzing the individual JBI files after the initial documentation phase.

## Important Patterns & Preferences

- The program follows a modular structure using separate JBI files for distinct tasks (setup, master control, welding sections, transitions).
- Macros (`SHIFT-R1.JBI`, `WEAVING.JBI`) are present, suggesting their use for specific, reusable functions like layer shifting or weld weaving patterns.
- The programming language is Yaskawa INFORM III.

## Learnings & Insights

## Learnings & Insights

- `MASTER-START-ML.JBI` orchestrates calls and passes arguments (`ARGFn`) to welding jobs.
- Welding jobs (`BOVEN`/`ONDER`) use arguments (`IARG#(1)`) to increment separate global target pass counters (`B096` for BOVEN, `B097` for ONDER).
- They use separate global current pass counters (`B098` for BOVEN, `B099` for ONDER).
- Both jobs loop internally based on their respective counters (`IF B09x < B09y`), executing multiple passes per call.
- Each pass configuration (`*n`) calls macros (`MJ#(1)` for shift, `MJ#(2)` for weave) with pass-specific arguments.
- `BOVEN` uses externally defined `P001`/`P002` directly for welding.
- `ONDER` dynamically constructs `P003`/`P004` using XYZ from `P001`/`P002`, a Z-offset, and local orientations.
- `ONDER` has more pass configurations than `BOVEN`.
- Weld speeds differ slightly between BOVEN and ONDER.
- `MACRO1 MJ#(1)` (`SHIFT-R1.JBI`) applies pass-specific 6D offsets via `SFTON P111 UF#(15)`.
- `MACRO1 MJ#(2)` (`WEAVING.JBI`) sets weave parameters (Amp, Freq, Angle) into `D080-D082` for `WVON`.
- `MACRO1 MJ#(0)` (`ESAB1JOB.JBI`) selects ESAB weld schedule (10/11) and checks errors.
- Transition jobs (`VAN-*.JBI`) use fast `MOVJ` commands through predefined `PULSE` points.
- `TESTUF-ARNE.JBI` defines `UF#(15)` and global weld points `P001`/`P002` based on taught points.
- `DOUT OT#(46) ON` within the welding jobs (`BOVEN`/`ONDER`) activates the cooling system. (Clarified by user). Note: No corresponding `DOUT OT#(46) OFF` was found in the analyzed files.
