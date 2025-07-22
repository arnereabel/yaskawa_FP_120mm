# YRC1000/DX Controller Weaving Adjustment Function (WVADJ)

*(Combined information from ATI_Funktion DX200 Weaving adjustment_GB02.pdf and Weaving Adjustment-DX (German).pdf)*

## 1. Purpose / Outline

The Weaving Adjustment function, also referred to as "Compensation of weaving root gap", allows the robot to automatically adjust weaving parameters (amplitude, frequency, speed) during a weld pass to compensate for changes in the groove width or gap.

*   **Wider Groove:** Typically increases amplitude, decreases frequency (Method 0) or increases frequency (Method 1), and decreases speed (Method 0) or increases speed (Method 1).
*   **Narrower Groove:** Typically decreases amplitude, increases frequency (Method 0) or decreases frequency (Method 1), and increases speed (Method 0) or decreases speed (Method 1).

*(Note: The mechanism for detecting the groove width change is not detailed in these documents.)*

## 2. Enabling the Function

The function is enabled via parameter:

*   **`FD61` (or `FD061`):** Set to `1` to enable the Weaving Adjustment function. (Default is `0`, disabled).

## 3. WVADJ INFORM Instruction

This instruction is used within a job to define how the weaving parameters should be adjusted over a specific segment.

**Syntax:**

`WVADJ STD=<StdVal> ST=<StartVal> END=<EndVal> LEN=<Length> ADJMD=<Method>`

**Parameters:**

*   **`WVADJ`:** The instruction itself.
*   **`STD=<StdVal>`:** Standard Value (likely the nominal or initial amplitude/gap value, unit: mm).
*   **`ST=<StartVal>`:** Start Value (amplitude/gap value at the beginning of the adjustment segment, unit: mm).
*   **`END=<EndVal>`:** End Value (target amplitude/gap value at the end of the adjustment segment, unit: mm).
*   **`LEN=<Length>`:** Length over which the adjustment occurs (distance along the weld path, unit: mm).
*   **`ADJMD=<Method>`:** Adjustment Method selector:
    *   `0`: Begrenzte Methode (Limited Method)
    *   `1`: Zuwachsmethode (Incremental Method)

## 4. Adjustment Methods (`ADJMD`)

### 4.1 Limited Method (`ADJMD=0`)

This method adjusts amplitude and speed while keeping frequency constant.

*   **Weaving Amplitude:** Increases linearly from `ST` value towards `END` value over the distance `LEN`.
*   **Weaving Frequency:** Remains constant (likely based on the initial `WVON` or `COMARCON` setting).
*   **Welding Speed:** Decreases linearly over the distance `LEN`. The rate of decrease depends on the difference between `END` and `ST` values relative to `LEN`.

**Example (from German PDF):**
*   Initial: AMP=2.0mm, FREQ=3.0Hz, Speed=40.0cm/min
*   WVADJ: STD=2.0, ST=2.0, END=6.0, LEN=50.0, ADJMD=0
*   Result: Amplitude increases from 2.0mm to 6.0mm over 50mm. Speed decreases from 40.0cm/min to a calculated 13.3cm/min over 50mm. Frequency remains 3.0Hz.

*(See Diagram in German PDF, Page 4)*

### 4.2 Incremental Method (`ADJMD=1`)

This method adjusts amplitude, frequency, and speed.

*   **Weaving Amplitude:** Increases linearly from `ST` value towards `END` value over the distance `LEN`.
*   **Weaving Frequency:** Increases (wird schneller) over the distance `LEN`. *(Rate/calculation not specified)*.
*   **Welding Speed:** Increases linearly over the distance `LEN`. The rate of increase depends on the difference between `END` and `ST` values relative to `LEN`.

**Example (from German PDF):**
*   Initial: AMP=2.0mm, FREQ=3.0Hz, Speed=40.0cm/min
*   WVADJ: STD=2.0, ST=2.0, END=6.0, LEN=50.0, ADJMD=1
*   Result: Amplitude increases from 2.0mm to 6.0mm over 50mm. Speed increases from 40.0cm/min to a calculated 48cm/min over 50mm. Frequency also increases.

*(See Diagram in German PDF, Page 5)*

## 5. Related Parameters (from English PDF)

These parameters likely influence the behavior and limits of the adjustment, although their exact interaction with the `WVADJ` instruction isn't fully detailed in the provided documents:

*   **`S2C472`:** Specify changing weaving frequency (Likely related to `ADJMD=1`).
*   **`S2C676`:** Specify limitation of Groove compensate weaving (Likely sets overall limits on how much parameters can change).
*   **`S2C943`:** Under limitation of groove width [micrometer] (Minimum detected groove width for adjustment?).
*   **`S2C944`:** Over limitation of groove width [micrometer] (Maximum detected groove width for adjustment?).
