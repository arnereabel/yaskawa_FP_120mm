# Technical Context

*This file details the technologies used, development environment setup, technical constraints, dependencies, and tool usage patterns.*

## Technologies Used

- **Robot System:** Yaskawa Industrial Robot (Controller model likely YRC1000 based on manual names, specific robot model unknown).
- **Programming Language:** Yaskawa INFORM III (likely using the **Expanded Instruction Set** due to `P[LB000]` usage).
- **File Format:** `.JBI` (Job Instruction) files for program logic.
- **Welding Equipment:** ESAB welder (confirmed by `ESAB1JOB.JBI` logic).
- **Internal Units:**
    - Position XYZ: 0.001 mm
    - Position Rotation: 0.0001 deg
    - Weave Vars (D080-D082): Likely 0.1mm (Amp), 0.1Hz (Freq), 0.1deg (Angle) based on *10 scaling in `WEAVING.JBI`.

## Development Setup

- **Programming Interface:** Typically done via the Yaskawa robot programming pendant.
- **Offline Software (Optional):** Software like Yaskawa MotoSim EG-VRC allows for offline programming, simulation, and debugging.
- **Text Editor (for offline):** Any text editor can be used to view/edit JBI files, but syntax understanding is crucial.
- **Knowledge Requirement:** Requires understanding of INFORM III syntax, robot coordinate systems (Joint, Base, Robot, User, Tool), motion commands, I/O handling, and job/macro structure.

## Technical Constraints

- **Robot Kinematics:** Limited by the robot's reach, axis limits, and potential singularity points.
- **Welding Physics:** Governed by material properties, joint preparation, gas flow, wire feed speed, voltage, current, travel speed, and heat input limitations.
- **Workpiece Geometry:** The specific dimensions and accessibility of the 120mm X-seam.
- **Controller Limitations:** Memory capacity for jobs/variables, processing speed.
- **Safety Standards:** Must adhere to robot safety regulations.
- **Collision Avoidance:** Paths must be carefully planned, especially during transitions.

## Dependencies

- **Yaskawa Controller:** Specific model (e.g., YRC1000) and its INFORM III version/capabilities.
- **Robot Arm:** Specific model and its physical characteristics.
- **Welding Power Source:** Specific model (e.g., ESAB) and its communication interface with the robot controller.
- **Tool Center Point (TCP):** Accurate definition of the welding torch TCP is critical.
- **User Frame:** Accurate definition of the workpiece position relative to the robot base.

## Tool Usage Patterns

- **Job Files (`.JBI`):** Used for main program flow and distinct operational sequences (master, welding, transitions).
- **Macro Files (`.JBI`):** Used for reusable subroutines (shifting, weaving, potentially welder control). Stored in a separate `macro jbi` subfolder.
- **Documentation:** Text files (`.txt`) stored in a `docs` subfolder alongside JBI files.
- **Naming Conventions:** Seem descriptive (e.g., `MULTILAYER-TESTAFLOOP-BOVEN`, `VAN-ONDER-NAAR-BOVEN`), aiding readability.
- **Version Control:** Currently managed within this workspace, potentially using Git (no `.git` folder visible in root, but could be higher up or not used). `CHANGELOG.md` exists.
