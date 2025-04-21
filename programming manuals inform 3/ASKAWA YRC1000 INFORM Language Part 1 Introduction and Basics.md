# YASKAWA YRC1000 INFORM Language - Part 1: Introduction and Basics

## 1. Overview of INFORM

INFORM is the robot programming language used with the YRC1000 controller system from YASKAWA. It's designed to provide comprehensive control over industrial robot operations.

### 1.1 INFORM Components

INFORM consists of two main components:
- **Instructions**: Commands that execute operations and processing
- **Additional items**: Tags and numeric data that specify conditions

Example of an INFORM instruction:
```
MOVJ VJ=50.00
```
Where:
- `MOVJ` is the instruction (move joints)
- `VJ=50.00` is the additional item (velocity of joints at 50%)

### 1.2 Types of Instructions

INFORM instructions are categorized by their function:

| Type | Description | Examples |
|------|-------------|----------|
| I/O Instructions | Control input/output signals | DOUT, WAIT, DIN |
| Control Instructions | Control program flow | JUMP, CALL, TIMER |
| Operating Instructions | Perform variable operations | ADD, SET, MUL |
| Move Instructions | Control robot movement | MOVJ, MOVL, MOVC |
| Shift Instructions | Shift teaching positions | SFTON, SFTOF |
| Adhering Instructions | Modify other instructions | IF, UNTIL |
| Work Instructions | Control specific applications | ARCON, WVON (welding) |

### 1.3 Instruction Sets

INFORM offers three language levels (instruction sets) with increasing capabilities:

1. **Subset Instruction Set**
   - Limited to frequently used instructions
   - Easier operation with fewer options

2. **Standard Instruction Set**
   - All INFORM instructions available
   - Limited use of variable types in additional items

3. **Expanded Instruction Set**
   - All INFORM instructions available
   - Full support for local variables and array variables
   - Most flexible but more complex

To select an instruction set:
1. Select {SETUP} under main menu
2. Select {TEACHING COND.}
3. Select "LANGUAGE LEVEL"
4. Choose the desired instruction set

### 1.4 Variables in INFORM

INFORM supports various variable types:

#### Position Variables
- Robot axis: P000-P127
- Base axis: BP000-BP127
- Station axis: EX000-EX127

Position variables can store position data as pulse type or XYZ type.

#### Data Type Variables
- Byte type (B): 8-bit integer (0-255)
- Integer type (I): 32-bit integer
- Double-precision type (D): 64-bit integer
- Real-number type (R): Floating point
- String type (S): Character strings

#### Local Variables
Available only in the Expanded instruction set, prefixed with "L":
- LB: Local byte variable
- LI: Local integer variable
- LD: Local double-precision variable
- LR: Local real-number variable
- LS: Local string variable
- LP: Local position variable
- LBP: Local base position variable
- LEX: Local station position variable

#### Array Variables
Available only in the Expanded instruction set, using square brackets:
- B[n]: Byte array
- I[n]: Integer array
- And so on for all types

Example using array variables:
```
FOR I000 START=10 TO 29
    SET P[I000] P0010
NEXT I000
```

#### Expanded Position Type Variables
The expanded position type variable (PX) depends on the control group in the job:
- When control group is R1: PX000 indicates P000
- When control group is R1 + B1: PX000 indicates P000 and BP000
- When control group is R1 + B1 + ST1: PX000 indicates P000 + BP000 + EX000

### 1.5 Registration of Instructions

To register instructions in a job:

1. In the job content window, press [INFORM LIST]
2. Select the desired instruction group from the dialog box
3. Select the specific instruction 
4. The instruction appears in the input buffer line
5. Press [ENTER] to register the instruction
6. To insert an instruction, press [INSERT] before pressing [ENTER]

### 1.6 Learning Function

The learning function helps minimize repetitive instruction registration by remembering previously used additional items.

To enable/disable the learning function:
1. Select {SETUP} under main menu
2. Select {TEACHING COND.}
3. Move cursor to "INSTRUCTION INPUT LEARNING"
4. Press [SELECT] to toggle between "VALID" and "INVALID"

### 1.7 Detail Edit Window

All instructions have a detail edit window for modifying additional items:
- Access by selecting an instruction and pressing [SELECT]
- Shows all available tags for the instruction
- Allows adding, modifying, or deleting additional items
- Provides icons for altering data types

### 1.8 INFORM Syntax

INFORM syntax is represented in flowcharts showing the order and connectivity of instruction elements:

- Instruction (e.g., MOVJ)
- Tags (e.g., VJ=)
- Data (e.g., joint speed percentage)
- End of instruction

Tags can be:
- Indispensable (must be included)
- Omittable (can be left out)
- Selectable (one of several options must be chosen)

## 2. Structured Language in INFORM

INFORM supports structured programming to make code more readable and maintainable.

### 2.1 Types of Structured Language

1. **Selection Processing**
   ```
   SWITCH to CASE to DEFAULT to ENDSWITCH
   ```

2. **Repetition Processing**
   ```
   WHILE to ENDWHILE
   FOR to NEXT
   ```

3. **Sequential Processing**
   ```
   IFTHEN to ELSEIF to ELSE to ENDIF
   ```

### 2.2 Registering Structured Language

Structured language elements must follow specific patterns:
- ENDIF is automatically registered with IFTHEN
- ENDSWITCH is automatically registered with SWITCH
- ENDWHILE is automatically registered with WHILE
- NEXT is automatically registered with FOR

### 2.3 Editing Structured Language

Editing structured language requires understanding how elements are connected:
- Editing a FOR instruction will reflect changes in its paired NEXT instruction
- Deleting structural elements may delete a block of code (e.g., deleting WHILE deletes everything to ENDWHILE)
- Cannot copy/cut structural elements without their pairs

### 2.4 Multiplexing Structured Language

Structured language can be nested (multiplexed):
```
FOR I000=1 TO 10
    FOR I001=1 TO 20
        INC B000
    NEXT I001
NEXT I000
```

Parameter S2C694 sets the maximum nesting level (default is 10).

## 3. Basic I/O Instructions

### 3.1 DOUT - Digital Output

Controls general-purpose output signals:

```
DOUT OT#(12) ON     // Turn ON output signal 12
DOUT OG#(3) B000    // Set output group 3 according to bit pattern in B000
DOUT OGU#(6) D000   // Set user output group 6 according to bit pattern in D000
```

#### Syntax Options:
- `OT#(n)`: Single output point (1-4096)
- `OG#(n)`: Output group of 8 points (1-512)
- `OGH#(n)`: Output group of 4 points (1-1024)
- `OGU#(n)`: User-defined output group (1-64)

#### Status Options:
- `ON/OFF`: Direct status setting
- `B variable`: Status determined by least significant bit
- `INVERT`: Inverts current status

### 3.2 DIN - Digital Input

Reads signal status into variables:

```
DIN B016 IN#(12)     // Read input 12 status into B016
DIN B002 OG#(8)      // Read status of output group 8 into B002
DIN D003 IGU#(9)     // Read status of user input group 9 into D003
```

#### Source Options:
- `IN#(n)`: Input signals (1-4096)
- `IG#(n)`: Input groups of 8 points (1-512)
- `IGH#(n)`: Input groups of 4 points (1-1024)
- `OT#(n)`, `OG#(n)`, `OGH#(n)`: Output signals/groups
- `SIN#(n)`, `SOUT#(n)`: Specific inputs/outputs
- `IGU#(n)`, `OGU#(n)`: User-defined groups

### 3.3 WAIT - Wait for Signal

Pauses execution until specified signal conditions are met:

```
WAIT IN#(12)=ON           // Wait until input 12 is ON
WAIT SIN#(B000)=B002 T=3.0 // Wait for specific input match with timeout
WAIT IGH#(2)<>5           // Wait until input group 2 is not equal to 5
```

#### Additional Options:
- `T=time`: Timeout in seconds (proceeds after timeout even if condition not met)
- Comparison operators: `=`, `<>`, `>`, `>=`, `<`, `<=`

### 3.4 PULSE - Output Pulse Signal

Outputs a pulse signal for specified time:

```
PULSE OT#(128)             // Output 0.3-second pulse to output 128
PULSE OT#(B000) T=1.0      // Output 1.0-second pulse to output in B000
PULSE OG#(3) B000          // Output pulse to output group based on bit pattern
```

#### Key Features:
- Default pulse time is 0.3 seconds if not specified
- Continues to next instruction without waiting for pulse completion
- Sequential PULSE to same output waits for completion of previous pulse

### 3.5 AOUT - Analog Output

Controls analog output ports:

```
AOUT AO#(1) I000           // Output voltage value in I000 to analog port 1
```

Voltage range: -14.00V to +14.00V (units: 0.01V)

### 3.6 ARATION/ARATIOF - Analog Output Rate Control

Controls analog output corresponding to motion speed:

```
ARATION AO#(1) BV=7.00 V=150.0 OFV=-10.0  // Configure analog output 1
ARATIOF AO#(1)                            // Cancel rate control
```

#### Parameters:
- `BV=`: Basic voltage at specified speed
- `V=`: Reference speed for basic voltage
- `OFV=`: Offset voltage at zero speed

### 3.7 ANTOUT - Anticipation Output

Adjusts timing of signal output (requires parameter S4C008):

```
ANTOUT AT#(1) ON ANT=-0.5   // Output signal 1 ON 0.5s before reaching position
```

#### Features:
- Advanced signal output (negative time): Signal before reaching position
- Delayed signal output (positive time): Signal after reaching position
- Can be distance-based instead of time-based
