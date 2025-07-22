# Weaving Condition Files (DX100/YRC1000)

*(Based on Weaving Condition Files.txt)*

The controller supports 255 Weaving Condition files (WEAV files) that can be set up with various patterns. The direction of patterns is based on a default coordinate system or taught reference points.

## Displaying & Editing WEAVE Files

1.  Navigate to `{ARC WELDING}` -> `{WEAVING}`.
2.  Use the `[PAGE]` key or the `{PAGE}` button (enter number + `[ENTER]`) to select the desired file number (1-255).
3.  The `WVON WEV#(X)` instruction can also be opened using the Direct Open key when the cursor is on the instruction.

## Weaving Condition File Parameters

*   **`WEAVING COND NO.`:** Displays the file number (1-255).
*   **`MODE`:** Selects the oscillation mode:
    *   `Single`
    *   `Triangular`
    *   `L-type`
*   **`SMOOTH`:** Specifies whether to use smoothing (corner rounding) for the selected mode (`USED`/`UNUSED`).
*   **`SPEED TYPE`:** Defines how weaving speed is determined:
    *   `FREQUENCY`: Speed defined in Hz (cycles per second).
    *   `MOVING TIME`: Speed defined by absolute time for each weave segment.
*   **`FREQUENCY`:** Weaving frequency in Hz (0.1 to 5.0 Hz). Only active when `SPEED TYPE` is `FREQUENCY`. Max frequency depends on amplitude.
*   **`PATTERN`:** Defines the shape and size of the weave based on the selected `MODE`.
    *   **`Amplitude` (`Single` Mode):** Half the width of the weave (TCP movement, not bead width). Affected by `SMOOTH` and `FREQUENCY`.
    *   **`Vertical` / `Horizontal` (`L` or `Triangle` Mode):** Specifies the length of the fillet legs.
*   **`ANGLE`:** Interpretation depends on `MODE`.
    *   **`Single` Mode:** Offset angle from a flat horizontal weave (0 = flat, 30 for lap, 45 for T-joint). Relative to Robot Frame by default, changeable via Reference Points. If `UNUSED`, defaults to 45 deg.
    *   **`L` or `Triangle` Mode:** Included angle between Vertical and Horizontal legs (e.g., 90 for a 90-deg T-joint).
*   **`TRAVEL ANGLE`:** Advances the upper or lower side of the weave.
    *   `-0.1` to `-90.0`: Push angle.
    *   `0.1` to `90.0`: Drag angle.
*   **`TIMER (Mode Selection)`:** Defines pause behavior at the end of weave segments.
    *   `Weave Stop`: Weaving pauses, linear robot motion continues.
    *   `Robot Stop`: Both weaving and linear robot motion pause.
*   **`MOVING TIME`:** Specifies the time for each segment. Only active when `SPEED TYPE` is `MOVING TIME`.
*   **`STOP TIMER`:** Time delay (0 = no delay) at the end of each segment, linked to `TIMER (Mode Selection)`.

## Programming Weaving Instructions

### `WVON` (Weave On)

Starts weaving. Can be programmed in two ways:

1.  **Using Weave File (`WVON WEV#(X)`):**
    *   From `{INFORM LIST}` -> `{DEVICE}` -> `{WVON}`.
    *   Select `WEV#()`, enter file number (X = 1-255), press `[ENTER]`.
    *   Optional `DIR=` (Direction) can be set in Detail Edit screen (0=normal, 1=reversed).
2.  **Using In-Line Tags (`WVON AMP=... FREQ=...` etc.):**
    *   From `{INFORM LIST}` -> `{DEVICE}` -> `{WVON}`.
    *   Press `[SELECT]` on `WVON` to enter Detail Edit.
    *   Select `WEV#()` and press `[SELECT]` again to switch to tag mode.
    *   Select `AMP=`, `FREQ=`, `ANGL=`, `DIR=` and enter values.
    *   *Note:* In-line tags are limited to `Single` mode weaves only. If `ANGL` is `UNUSED`, it defaults to 45 deg.

### `WVOF` (Weave Off)

Stops weaving.

1.  From `{INFORM LIST}` -> `{DEVICE}` -> `{WVOF}`.
2.  Press `[ENTER]`.

## Default Coordinate System for Weaving

*   **Wall Direction:** Defaults to the Z+ direction of the Robot Frame.
*   **Horizontal Direction:** Determined by the side of the wall where the approach point (step before `WVON`) is programmed.
*   *Note:* This default system might be inappropriate depending on workpiece mounting/shape. Reference Points may be needed.

## Reference Points (REFP)

Used when the default coordinate system is insufficient or the approach point cannot be placed correctly.

*   **`REFP1`:** Defines the **Wall direction**. Programmed on the desired wall surface. Needed if the wall is not parallel to the Robot Z-axis.
*   **`REFP2`:** Defines the **Horizontal direction** (which side of the wall to weld on). Overrides the approach point location for determining the side. Needed if the approach point cannot be programmed on the correct side (may prevent "sewing machine" weave).

### Teaching/Managing Reference Points

*   **Teach:**
    1.  Cursor to line above `WVON`.
    2.  Jog TCP to the desired REFP location.
    3.  Press `[INSERT]`.
    4.  Press `[REFP]` key (Numeric Keypad '0').
    5.  Select REFP number (1-8), press `[ENTER]`.
    6.  Press `[ENTER]` again.
*   **Move Robot to REFP:** Cursor on REFP line, hold `[REFP]` key, press `[FWD]`.
*   **Modify:** Cursor on REFP line, jog robot to new position, press `[REFP]` key, press `[MODIFY]`, press `[ENTER]`.
*   **Delete:** Cursor on REFP line, forward robot to REFP, press `[DELETE]`, press `[ENTER]`.

## Prohibiting Weave Operation

Weaving can be disabled during test runs (FWD/INTERLOCK+TEST START in Teach, CHECK mode in Play).

1.  Set desired mode (TEACH or PLAY).
2.  From Job Content screen: `{UTILITY}` -> `{SET-UP SPECIAL RUN}`.
3.  Set `WEAVE PROHIBIT IN TEST-RUN/NEXT` (Teach) or `WEAVE PROHIBIT IN CHK-RUN` (Play) to `VALID`.
