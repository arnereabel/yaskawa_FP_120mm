# System Patterns

*This file documents the overall system architecture, key technical decisions, design patterns employed, component relationships, and critical implementation paths.*

## System Architecture Overview

The system is a Yaskawa robot program controlling a multi-layer welding process. It follows a modular architecture where a master job (`MASTER-START-ML.JBI`) coordinates calls to specialized sub-jobs and potentially macros. Key components include:
- User Frame Setup (`TESTUF-ARNE.JBI`)
- Master Control Logic (`MASTER-START-ML.JBI`)
- Welding Logic for Top/Bottom Sections (`MULTILAYER-TESTAFLOOP-BOVEN.JBI`, `MULTILAYER-TESTAFLOOP-ONDER.JBI`)
- Transition Logic between Sections (`VAN-BOVEN-NAAR-ONDER.JBI`, `VAN-ONDER-NAAR-BOVEN.JBI`)
- Supporting Macros for specific functions like shifting (`SHIFT-R1.JBI`) or weaving (`WEAVING.JBI`).

## Key Technical Decisions

- **Modular Programming:** Utilizing separate JBI files (jobs and macros) for different functionalities enhances organization and maintainability.
- **User Frame Definition:** Implementing a dedicated job for user frame setup ensures positional accuracy relative to the workpiece.
- **Sectional Welding:** Dividing the welding process into top and bottom sections simplifies the logic for each part.
- **Explicit Transitions:** Using specific jobs for transitions between sections likely improves safety and path planning.
- **Potential Macro Usage:** Employing macros for tasks like layer shifting or weaving promotes code reuse.

## Design Patterns in Use

- **Master-Slave/Coordinator:** The `MASTER-START-ML.JBI` acts as a coordinator, calling other jobs (slaves) to perform specific tasks.
- **Modular Design:** Breaking down the complex task into smaller, manageable JBI files.
- **Subroutine/Macro:** Using JBI macros (`SHIFT-R1.JBI`, `WEAVING.JBI`) for reusable code blocks.
*(Further patterns may become apparent after code analysis.)*

## Component Relationships

```mermaid
graph TD
    subgraph Initialization
        UF[TESTUF-ARNE.JBI User Frame Setup]
    end

    subgraph Main Execution
        Master[MASTER-START-ML.JBI Master Control]
        WeldOnder[MULTILAYER-TESTAFLOOP-ONDER.JBI Bottom Weld]
        WeldBoven[MULTILAYER-TESTAFLOOP-BOVEN.JBI Top Weld]
        TransUp[VAN-ONDER-NAAR-BOVEN.JBI Transition Up]
        TransDown[VAN-BOVEN-NAAR-ONDER.JBI Transition Down]
    end

    subgraph Macros
        Shift[SHIFT-R1.JBI Layer Shift Macro]
        Weave[WEAVING.JBI Weaving Macro]
        Esab[ESAB1JOB.JBI ESAB Macro?]
    end

    UF --> Master
    Master --> WeldOnder
    WeldOnder --> TransUp
    TransUp --> WeldBoven
    WeldBoven --> TransDown # Optional/Looping?
    TransDown --> WeldOnder # Optional/Looping?

    WeldOnder --> Shift # Potentially
    WeldOnder --> Weave # Potentially
    WeldOnder --> Esab # Potentially
    WeldBoven --> Shift # Potentially
    WeldBoven --> Weave # Potentially
    WeldBoven --> Esab # Potentially

    style Master fill:#f9f,stroke:#333,stroke-width:2px
    style UF fill:#ccf,stroke:#333,stroke-width:2px
    style WeldOnder fill:#cfc,stroke:#333,stroke-width:2px
    style WeldBoven fill:#cfc,stroke:#333,stroke-width:2px
    style TransUp fill:#ffc,stroke:#333,stroke-width:2px
    style TransDown fill:#ffc,stroke:#333,stroke-width:2px
    style Macros fill:#eee,stroke:#666,stroke-width:1px,stroke-dasharray: 5 5
```

**Description:**
1.  **Initialization (`TESTUF-ARNE.JBI`):**
    *   Guides operator to teach Origin (`C00000`) and XX-axis (`C00001`) points using `MOVL`/`PAUSE`.
    *   Captures taught points into `PX124` (Origin) and `PX125` (XX-axis) using `GETPOS`.
    *   Sets global `P001 = PX124` (base weld start point).
    *   Sets global `P002 = PX125` (base weld end point).
    *   Calculates a third point (`P126`) offset 100mm in Y from the origin (`PX124`).
    *   Defines `UF#(15)` using the 3-point method: `MFRAME UF#(15) PX124 PX125 P126`.
2.  **Master Control (`MASTER-START-ML.JBI`):**
    *   Initializes global Byte variables B096, B097, B098, B099 to 0.
    *   Executes a fixed sequence of calls to welding and transition jobs.
    *   Passes arguments (`ARGFn`) to welding jobs, indicating the number of passes to add to the target count for that section.
3.  **Welding Jobs (`BOVEN`/`ONDER`):**
    *   Receive `IARG#(1)` and add it to their respective global target pass counters (`B096` for BOVEN, `B097` for ONDER).
    *   Use respective global current pass counters (`B098` for BOVEN, `B099` for ONDER).
    *   Loop internally (`JUMP ... IF B09x < B09y`), executing passes until the current counter reaches the target counter.
    *   Inside the loop, jump to a pass-specific configuration block (`*n`) based on the current pass counter (`B098` or `B099`).
    *   Each configuration block (`*n`) calls macros:
        *   `MACRO1 MJ#(0)` (`ESAB1JOB.JBI`): Takes 1 argument (10 for BOVEN, 11 for ONDER), sets ESAB weld schedule via `MREG#(188)`, verifies selection (`IN#(34)`/`MREG#(182)`), and checks/resets welder errors (`IN#(49-52)`).
        *   `MACRO1 MJ#(2)` (`WEAVING.JBI`): Takes 3 arguments (Amplitude[mm?], Frequency[Hz?], Angle[deg?]), scales them (*10, likely to 0.1 units), stores them in `D080`, `D081`, `D082`.
        *   `MACRO1 MJ#(1)` (`SHIFT-R1.JBI`): Takes 6 arguments (X[mm],Y[mm],Z[mm],Rx[deg],Ry[deg],Rz[deg] shift), scales them (*1000 for XYZ, *10000 for Rot), stores in `P111`, and activates `SFTON P111 UF#(15)`.
    *   Execute common welding sequence:
        *   `DOUT OT#(46) ON`: Activate cooling system.
        *   `REFP 1`/`REFP 2`: Define reference points for weave orientation.
        *   `MOVL` to approach point.
        *   `MOVL` to start point (`P001` for BOVEN, constructed `P003` for ONDER).
        *   `ARCON`: Start arc using default conditions from selected ESAB schedule (10 or 11).
        *   `WVON AMP=D080 FREQ=D081 ANGL=D082`: Start weaving using parameters set by `MJ#(2)`. Weave pattern likely default from schedule/system.
        *   `MOVL` to end point (`P002` for BOVEN, constructed `P004` for ONDER) at specified speed `V=`.
        *   `WVOF`: Stop weaving.
        *   `ARCOF`: Stop arc using default conditions from selected ESAB schedule.
        *   `MOVL`/`MOVJ` to retract points.
    *   `INC B098` or `INC B099`: Increment current pass counter.
    *   `SFTOF`: Disable shift offset applied by `MJ#(1)`.
    *   Loop back if `B09x < B09y`.
4.  **Transition Jobs (`VAN-*.JBI`):** Use fast (`VJ=100.00`) `MOVJ` commands through predefined `PULSE` coordinate points. `VAN-ONDER-NAAR-BOVEN` reverses the path of the last 5 points of `VAN-BOVEN-NAAR-ONDER`.
5.  **Macros (`SHIFT-R1.JBI`, `WEAVING.JBI`, `ESAB1JOB.JBI`):** Provide reusable functions invoked via `MACRO1 MJ#(n)`. `SHIFT-R1.JBI` is `MJ#(1)`, `WEAVING.JBI` is `MJ#(2)`, `ESAB1JOB.JBI` is `MJ#(0)`.

## Critical Implementation Paths

- **Multi-Layer Logic:** Coordination via global counters (`B096`/`B098`, `B097`/`B099`) and `ARGFn`. Pass-specific offsets applied using `MACRO1 MJ#(1)` (`SHIFT-R1.JBI`). Pass-specific weave parameters set by `MACRO1 MJ#(2)` (`WEAVING.JBI`).
- **Start/End Point Handling:** Base points `P001`/`P002` set in `TESTUF-ARNE.JBI`. `ONDER` job dynamically constructs `P003`/`P004`.
- **Pass Configuration:** Understanding the specific 6 arguments (shift) and 3 arguments (weave) passed to macros in each `*n` block defines the weld fill strategy.
- **Macro Functions:** `MJ#(0)` (ESAB Control), `MJ#(1)` (Shift), `MJ#(2)` (Weave) understood based on JBI analysis and manuals.
- **Welding Parameter Control:** ESAB Weld schedules 10 (BOVEN) and 11 (ONDER) selected via `MJ#(0)`. `ARCON`/`ARCOF` use default conditions from these schedules. Travel speed (`V=`) set in `MOVL` commands.
- **Transition Movements:** Use fast `MOVJ` commands through predefined `PULSE` coordinate points.
- **Global Variable Usage:** `B096`/`B098` track target/current passes for BOVEN. `B097`/`B099` track target/current passes for ONDER. `P001`/`P002` store base weld start/end. `P111` stores temporary shift vector. `D080-D082` store temporary weave parameters.
- **Coordinate Systems:** `TESTUF-ARNE.JBI` defines `UF#(15)`. Welding jobs operate in `UF#(15)`. Shift macro applies shift relative to `UF#(15)`. Transition jobs use `PULSE` coordinates.
