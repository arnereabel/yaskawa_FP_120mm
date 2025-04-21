# YASKAWA YRC1000 INFORM Language - Part 5: Shift Instructions and Special Functions

## 1. Shift Instructions

Shift instructions allow you to offset the robot's path without modifying the original teaching positions. This is valuable for adjusting positions based on workpiece variation or for creating multiple similar paths.

### 1.1 SFTON Instruction (Shift On)

The SFTON instruction begins a parallel shift operation.

#### Syntax:
```
SFTON [position] [coordinate system]
```

#### Examples:
```
SFTON P000 UF#(1)           // Shift by amount in P000 in user frame 1
SFTON P000 TF               // Shift by amount in P000 in tool frame
```

#### Parameters:

**Position Data:**
- P/LP/P[]/LP[] variable (robot axis offset)
- BP/LBP/BP[]/LBP[] variable (base axis offset)
- EX/LEX/EX[]/LEX[] variable (station axis offset)

**Coordinate System:**
- BF: Base coordinate system
- RF: Robot coordinate system
- TF: Tool coordinate system
- UF#(n): User coordinate system (1-63)

#### Multi-Robot Control:
For multi-robot systems, the RBn parameter specifies which robot performs the shift:
```
SFTON RB1 P000 RF           // Shift Robot 1 by P000 in robot coordinates
```

#### Notes:
- Affects all move instructions between SFTON and SFTOF
- Offset is a relative position in specified coordinate system
- Commonly used for part variations or creating patterns

### 1.2 SFTOF Instruction (Shift Off)

The SFTOF instruction ends a parallel shift operation.

#### Syntax:
```
SFTOF
```

#### Multi-Robot Control:
For multi-robot systems, the RBn parameter specifies which robot ends the shift:
```
SFTOF RB1                   // End shift for Robot 1
```

For systems with station axes:
```
SFTOF ST                    // End shift for station axes
```

#### Notes:
- Terminates the effect of the most recent SFTON
- All subsequent movements use original taught positions

### 1.3 MSHIFT Instruction (Matrix Shift)

The MSHIFT instruction calculates shift amounts based on the difference between two positions.

#### Syntax:
```
MSHIFT <Data 1> <coordinate system> <Data 2> <Data 3>
```

#### Examples:
```
MSHIFT PX010 BF PX000 PX001    // Calculate shift from PX000 to PX001 in base coordinates
```

#### Parameters:
- Data 1: PX variable to store calculated shift
- Coordinate system: BF, RF, TF, UF#(n), MTF
- Data 2: Reference position
- Data 3: Target position

#### Notes:
- Useful for creating shifts based on measured positions
- Often used with GETS to capture current positions
- Result can be used with SFTON to apply the calculated shift

### 1.4 3D Shift Instructions (Optional Function)

The 3D shift functions enable complex shifts involving both translation and rotation.

#### SFTON3D Instruction:
```
SFTON3D P000                   // Start 3D shift by amount in P000
```

#### SFTOF3D Instruction:
```
SFTOF3D                        // End 3D shift operation
```

#### +SFTON3D Instruction:
```
MOVL V=138 +SFTON3D P000       // Apply 3D shift to specific move
```

#### Notes:
- 3D shift maintains relative position/posture with base coordinates
- Enables translation and rotation simultaneously
- Used for multi-layer welding and complex path adjustments

## 2. Instructions that Adhere to Other Instructions

These instructions modify the behavior of other instructions, particularly move instructions.

### 2.1 IF Instruction

The IF instruction adds a conditional check to another instruction.

#### Syntax:
```
[instruction] IF [condition]
```

#### Examples:
```
JUMP *1 IF IN#(12)=ON          // Jump to label 1 if input 12 is ON
JUMP *2 IF D005<=D006          // Jump if D005 is less than or equal to D006
```

#### Condition Types:
- Input/output signals: `IN#(n)=ON`, `OT#(n)=OFF`
- Variables: `B000=5`, `I001>I002`, `D003<>0`
- Flags and timer flags: `FL000=ON`, `TF001=OFF`
- String comparisons: `S000="TEST"`

#### Comparison Operators:
- `=`: Equal to
- `<>`: Not equal to
- `>`: Greater than
- `>=`: Greater than or equal to
- `<`: Less than
- `<=`: Less than or equal to

### 2.2 UNTIL Instruction

The UNTIL instruction terminates a move instruction when a condition is met.

#### Syntax:
```
[move instruction] UNTIL [condition]
```

#### Example:
```
MOVJ VJ=50.00 UNTIL IN#(14)=ON    // Stop joint move when input 14 turns ON
```

#### Notes:
- Causes move to terminate early when condition is satisfied
- Robot immediately proceeds to next instruction
- Only works with input signals (IN#)
- Commonly used for search moves or early termination of paths

### 2.3 ENWAIT Instruction

The ENWAIT instruction executes the next instruction before reaching the end of a move.

#### Syntax:
```
[move instruction] ENWAIT T=3.00
[move instruction] ENWAIT DIS=100.0
```

#### Examples:
```
MOVL V=136 ENWAIT T=3.00        // Execute next instruction 3 seconds before reaching position
DOUT OT#(1) ON                 // This turns ON 3 seconds before reaching position

MOVL V=136 ENWAIT DIS=100.0     // Execute next instruction 100mm before reaching position
DOUT OT#(1) ON                 // This turns ON 100mm before reaching position
```

#### Parameters:
- T=time: Time before reaching position (seconds)
- DIS=distance: Distance before reaching position (mm)

#### Notes:
- Only affects the execution of the next non-move instruction
- Used for timing signals or operations before reaching a position
- Requires S2C714 parameter to be enabled

### 2.4 DIALSB Instruction

The DIALSB instruction defines the structure of dialogs shown by the DIALOG instruction.

#### Syntax:
```
DIALOG B000 DIALSB B001 B002 S000 B003 B004 S001
```

#### Parameters:
- B001, B002: Message X,Y coordinates
- S000: Message text
- B003, B004: Button X,Y coordinates
- S001: Button text

#### Notes:
- Used exclusively with DIALOG instruction
- Multiple DIALSB instructions can define multiple buttons
- Result of button press is stored in first variable of DIALOG

### 2.5 TRAP Instruction

The TRAP instruction defines error handling for move instructions.

#### Syntax:
```
[move instruction] TRAP *label
```

#### Example:
```
MOVL V=1500.0 TRAP *ERR        // Jump to *ERR label if error occurs during move
```

#### Notes:
- If limit exceeded, motion is interrupted without alarm
- Execution jumps to specified label
- Used for handling motion limits and errors gracefully

### 2.6 Conditional Expression Instructions

Several instructions allow more complex conditional evaluation:

#### IFEXPRESS:
```
[instruction] IFEXPRESS (IN#(1)=ON AND IN#(2)=ON)
```

#### FOREXPRESS:
```
WAIT FOREXPRESS (IN#(1)=ON AND IN#(2)=ON)
```

#### LOGICEXP:
```
SET FL0000 LOGICEXP (IN#(1)=ON AND IN#(2)=ON)
```

#### RELEXPR:
Used to specify conditional statements within other expressions.

#### Notes:
- Allow complex logical combinations with AND/OR
- Support parentheses for grouping conditions
- Up to 6 conditions can be combined in one expression

## 3. Application-Specific Instructions

INFORM includes specialized instructions for various applications.

### 3.1 Arc Welding Instructions

#### Start/End Welding:
```
ARCON ASF#(1)                  // Start welding with condition file 1
ARCON AC=220 AVP=100 T=0.50    // Start with specific settings
ARCOF AEF#(1)                  // End welding with condition file 1
```

#### Weaving Control:
```
WVON WEV#(2)                   // Start weaving with condition file 2
WVON AMP=5.0 FREQ=3.0 ANGL=40.0 // Start with specific settings
WVOF                           // End weaving
```

#### Welding Parameter Control:
```
ARCSET AC=250                  // Change welding current during welding
ARCCTS AC=150 AV=16.0 DIS=100.0 // Gradual parameter change at start
ARCCTE AC=150 AV=16.0 DIS=100.0 // Gradual parameter change at end
VWELD 6.0                      // Set welding voltage
AWELD 6.0                      // Set welding current
```

### 3.2 Handling Instructions

#### Tool Control:
```
HAND 1 ON                      // Turn tool 1 ON
HAND 1 OFF                     // Turn tool 1 OFF
HAND 1 ON ALL                  // Simultaneous control (3P solenoid)
```

#### Sensor Monitoring:
```
HSEN 1 ON                      // Check if sensor 1 is ON
HSEN 1 ON T=5.00               // Check for 5 seconds
HSEN 1 ON FOREVER              // Check indefinitely
```

### 3.3 General-Purpose Instructions

Used for material handling, press tending, and other applications:

```
TOOLON                         // Turn ON work instruction
TOOLOF                         // Turn OFF work instruction
```

### 3.4 Spot Welding Instructions

For spot welding applications:

```
GUNCL GUN#(1) T=2.00           // Apply pressure to gun 1 for 2 seconds
SPOT GUN#(1) MODE=2 WTM=5      // Execute spot welding sequence
STROKE GUN#(1) LONG            // Set gun 1 to full open
STRWAIT GUN#(1) LONG           // Wait until gun 1 is fully open
```

## 4. System Integration Features

INFORM provides several capabilities for system integration.

### 4.1 Job Queue Management

For sequential job execution:

```
CLEAR QUE                      // Clear all job queues
```

### 4.2 Signal Communication

Synchronization between robots and external controllers:

```
WAIT IN#(12)=ON                // Wait for input signal
DOUT OT#(1) ON                 // Set output signal
PULSE OT#(1) T=1.0             // Pulse output signal
```

### 4.3 File and Register Access

For data exchange with external systems:

```
SETFILE WEV#(1) (5) 3500       // Change setting in condition file
GETFILE D000 WEV#(1) (6)       // Get value from condition file
SETREG MREG#(5) I000           // Write to register
GETREG I000 MREG#(5)           // Read from register
```

### 4.4 Parameter Access

For advanced system configuration (macro jobs only):

```
GETPRM D000 KIND=4 PRMNO=800 GRPNO=1  // Get parameter
SETPRM KIND=5 PRMNO=67 D000           // Set parameter
```

## 5. Programming Techniques and Best Practices

### 5.1 Shift Operations

Shift operations provide powerful capabilities for flexible programming:

#### Parallel Path Shifting:
```
SFTON P000 UF#(1)              // Apply shift
[move instructions]            // Path is shifted
SFTOF                          // End shift
```

#### Multiple Shift Layers:
```
SFTON P000 UF#(1)              // Apply first shift
[move instructions]
SFTON P001 UF#(1)              // Apply second shift (cumulative)
[move instructions]
SFTOF                          // Remove second shift
[move instructions]
SFTOF                          // Remove first shift
```

#### Dynamic Shift Calculation:
```
GETS PX000 $PX000              // Get current position
GETS PX001 $PX001              // Get current position (after moving)
MSHIFT PX010 BF PX000 PX001    // Calculate shift
SFTON PX010 BF                 // Apply calculated shift
```

#### 3D Shift for Complex Operations:
```
SFTON3D P000                   // Apply 3D shift (translation and rotation)
[move instructions]
SFTOF3D                        // End 3D shift
```

### 5.2 Conditional Execution Patterns

Effective use of conditional instructions:

#### Selective Operations:
```
JUMP *SKIP IF IN#(1)=OFF       // Skip section if input off
[operations]
*SKIP
```

#### Smart Retry Logic:
```
*RETRY
[operation]
JUMP *SUCCESS IF IN#(10)=ON    // Check success condition
INC B000                       // Increment counter
JUMP *FAIL IF B000>3           // Fail after 3 attempts
TIMER T=1.00                   // Wait before retry
JUMP *RETRY                    // Try again
*SUCCESS
[success handling]
JUMP *END
*FAIL
[failure handling]
*END
```

#### Efficient Multi-Condition Checks:
```
DIN B001 IG#(1)                // Read group of signals at once
JUMP *CASE1 IF B001=1
JUMP *CASE2 IF B001=2
JUMP *CASE3 IF B001=4
JUMP *DEFAULT
```

### 5.3 Timing Control

Precise timing using various instructions:

#### Sequential Timing:
```
MOVL V=100 NWAIT               // Start moving
TIMER T=0.50                   // Wait 0.5 seconds during move
DOUT OT#(1) ON                 // Turn on output after 0.5 seconds
CWAIT                          // Wait for motion completion
```

#### Position-Based Timing:
```
MOVL V=100 ENWAIT DIS=50.0     // Execute next when 50mm from end
DOUT OT#(1) ON                 // Turn on output 50mm before end
```

#### Signal-Based Timing:
```
MOVL V=100 UNTIL IN#(1)=ON     // Stop when input detected
```

### 5.4 Modular Programming

Creating reusable job modules:

#### Parameter Passing:
```
CALL JOB:CHECK_PART (1, 2, 3)  // Call with parameters
```

In CHECK_PART job:
```
GETARG LB000 IARG#(1)          // Get parameter 1
GETARG LB001 IARG#(2)          // Get parameter 2
GETARG LB002 IARG#(3)          // Get parameter 3
```

#### Return Value Usage:
```
CALL JOB:MEASURE_PART
GETS D000 $RV                  // Get measurement result
```

In MEASURE_PART job:
```
[measuring operations]
RET D001                       // Return measurement
```

## 6. System-Specific Features

### 6.1 Multi-Robot Control

For coordinated robot systems:

```
MOVJ RB1 VJ=50.00              // Move Robot 1
MOVJ RB2 VJ=50.00              // Move Robot 2
```

Coordinated motion:
```
MOVL V=100 +MOVL V=100         // Synchronized linear motion
```

### 6.2 User Coordinate Systems

Creating and using user coordinate systems:

```
MFRAME UF#(1) PX000 PX001 PX002  // Create from 3 points
MOVL V=100 UF#(1)                // Move in user coordinates
SFTON P000 UF#(1)                // Shift in user coordinates
```

### 6.3 Tool Management

Tool definition and selection:

```
SETE P000 TL#(1)                 // Set tool number in position
MOVL V=100 TL#(2)                // Move with specific tool
```

## 7. Conclusion and Additional Resources

This guide covers the essential aspects of YASKAWA's INFORM language for the YRC1000 controller, with emphasis on shift instructions and special functions. 

The INFORM language provides a comprehensive framework for industrial robot programming, balancing low-level motor control with high-level application features. By understanding the core instructions and their interactions, programmers can create sophisticated robot applications for welding, handling, and other industrial processes.

Key strengths of INFORM include:

1. Flexible motion control with multiple interpolation methods
2. Powerful shift capabilities for adaptive programming
3. Comprehensive I/O handling for system integration
4. Application-specific instructions for common industrial tasks
5. Structured programming capabilities for complex logic

For complete details, refer to the full YRC1000 INFORM LANGUAGE manual and other application-specific documentation from YASKAWA.
