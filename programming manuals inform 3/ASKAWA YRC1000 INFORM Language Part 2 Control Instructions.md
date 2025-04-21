# YASKAWA YRC1000 INFORM Language - Part 2: Control Instructions

Control instructions in INFORM manage program flow and execution, allowing branching, looping, and modular programming.

## 1. JUMP Instruction

The JUMP instruction transfers program execution to a specified label or job.

### Syntax:
```
JUMP *label
JUMP LABEL:B000
JUMP JOB:jobname
JUMP B000 IF IN#(14)=ON
```

### Parameters:

| Parameter | Description |
|-----------|-------------|
| `*label` | Jump to a label within the current job |
| `LABEL:` | Jump to a label number specified by a variable |
| `JOB:` | Jump to another job |
| `B variable/I variable/D variable` | Variable containing job number |
| `IG#()` | Input group (jump to job number if not 0) |
| `QUE` | Jump to job in queue (requires queue function) |
| `JET#()` | Jump to job registered in job table |
| `S variable` | Jump to job specified by string variable |

### Additional Options:

- `LBLST=n`: Label search starting position (0=job tip, 1=current position, 2=end)
- `LBLDF=n`: Search direction (0=forward, 1=backward)
- `UF#()`: User coordinate number for relative job
- `IF` condition: Conditional jump

### Important Notes:
- JUMP does not store return information
- After JUMP to another job, execution continues in that job
- Label search is only within the current job

## 2. CALL Instruction

The CALL instruction temporarily transfers program execution to another job, then returns.

### Syntax:
```
CALL JOB:TEST1
CALL B000 IF IN#(14)=ON
CALL JOB:WAIT_INPUT (1, 5, 7)
```

### Parameters:
Similar to JUMP parameters, plus:
- Arguments: Up to 8 values can be passed to the called job

### Features:
1. **Job Stacking**: Information about the calling job is stored in the JOB stack
2. **Arguments**: Allows passing data to the called job
3. **Return Values**: The called job can return one value using RET

### Example with Arguments:
```
// Calling job
CALL JOB:WAIT_INPUT (1, 5, 7)

// Called job (WAIT_INPUT)
GETARG LB000 IARG#(1)  // Receive first argument
GETARG LB001 IARG#(2)  // Receive second argument
GETARG LB002 IARG#(3)  // Receive third argument
WAIT IN#(LB000) ON
WAIT IN#(LB001) ON
WAIT IN#(LB002) ON
RET
```

### Example with Return Value:
```
// Calling job
CALL JOB:ADD_REG (1, 2)
GETS I001 $RV          // Get return value

// Called job (ADD_REG)
GETARG LB000 IARG#(1)  // Get register 1
GETARG LB001 IARG#(2)  // Get register 2
GETREG LI000 MREG#(LB000)
GETREG LI001 MREG#(LB001)
ADD LI001 LI000
RET LI001              // Return sum
```

### Important Notes:
- Unlike JUMP, CALL returns to the calling job after execution
- RET instruction returns control to calling job
- CALL + RET with arguments simplifies code organization and reduces global variable usage

## 3. RET Instruction

The RET instruction returns program control from a called job to the calling job.

### Syntax:
```
RET
RET IF IN#(12)=ON
RET LI000
```

### Parameters:
- Optional IF condition: Return only if condition is true
- Optional return value: Any variable type except position variables

### Features:
- Returns control to the calling job at the instruction following the CALL
- Can return data to the calling job
- Can be conditional

## 4. TIMER Instruction

The TIMER instruction pauses program execution for a specified time.

### Syntax:
```
TIMER T=12.50
TIMER T=I002
```

### Parameters:
- `T=`: Time in seconds (0.01 to 655.35)
- Can use I/LI variables as time values (units: 0.01 seconds)

## 5. Label Definition (*)

Labels mark positions in a job for JUMP instructions to target.

### Syntax:
```
*LABEL_NAME
```

### Features:
- Labels can be up to 8 characters
- Only effective within the same job
- Cannot jump to identical labels in other jobs

## 6. Comment (')

Comments provide documentation within a job without affecting execution.

### Syntax:
```
'This is a comment
```

### Features:
- Up to 32 characters
- Helps document code and improve readability

## 7. NOP Instruction

The NOP (No Operation) instruction does nothing.

### Syntax:
```
NOP
```

### Features:
- Often used as the first line of a job
- Can be used as a placeholder

## 8. PAUSE Instruction

The PAUSE instruction temporarily stops job execution.

### Syntax:
```
PAUSE
PAUSE IF IN#(12)=ON
```

### Parameters:
- Optional IF condition: Pause only if condition is true

## 9. CWAIT Instruction

The CWAIT instruction releases NWAIT execution and waits for the next instruction line.

### Syntax:
```
CWAIT
```

### Usage with NWAIT:
```
MOVL V=100 NWAIT  // Start moving
DOUT OT#(1) ON    // Turn on output while moving
CWAIT              // Wait until movement completes
DOUT OT#(1) OFF   // Turn off output after movement
```

## 10. MSG Instruction

The MSG instruction displays a message on the programming pendant.

### Syntax:
```
MSG "Check workpiece position"
MSG S000
```

### Parameters:
- Message string (up to 32 characters)
- S variable containing the message

## 11. INPUT Instruction

The INPUT instruction displays a value input window for operator data entry.

### Syntax:
```
INPUT B000
INPUT I005
```

### Parameters:
- B/I/D/R variable to store the input value

### Behavior:
- Shows confirmation dialog
- If YES, displays numeric input window
- Value is stored in the specified variable

## 12. PRINT/CLS Instructions

These instructions control output to the terminal display.

### Syntax:
```
PRINT "TEST"
PRINT "TEST\n"
PRINT "TEST%d" B001
PRINT B001
CLS
```

### Features:
- PRINT: Displays text and variable values
- Formatting options: %d (decimal), %x (hex), %o (octal), %s (string), %f (real)
- CLS: Clears the terminal display

## 13. ABORT Instruction

The ABORT instruction suspends playback with a message.

### Syntax:
```
ABORT
ABORT IF B000=1
```

### Features:
- Stops job execution
- Displays "Robot is stopped by execution ABORT command"
- Job will not restart until reselected

## 14. SETUALM Instruction

The SETUALM instruction generates a user-defined alarm.

### Syntax:
```
SETUALM 8000 0
SETUALM 8000 "ALM" 0 SMODE=1
```

### Parameters:
- Alarm code (8000-8999)
- Optional alarm name
- Subcode (0-255)
- SMODE: Operation stop mode (0=All tasks stop, 1=No stop, 2=Current task stop)

## 15. DIALOG Instruction

The DIALOG instruction shows interactive dialogs during job execution.

### Syntax:
```
DIALOG B000 DIALSB B001 B002 S000 B003 B004 S001
```

### Structure:
- B000: Variable to store button selection result
- DIALSB: Dialog sub-instruction defining message and button
  - B001, B002: Message X,Y coordinates
  - S000: Message text
  - B003, B004: Button X,Y coordinates
  - S001: Button text

### Features:
- Can have multiple DIALSB sub-instructions (up to 5 buttons)
- Dialog blocks job execution until a button is pressed
- Button selection (1-5) is stored in the result variable

## 16. Structured Control Instructions

INFORM supports structured programming with several control structures:

### 16.1 SWITCH-CASE-DEFAULT-ENDSWITCH

Selects execution path based on variable value:

```
SWITCH I000
CASE 1
    DOUT OT#(1) ON
CASE 2
    DOUT OT#(2) ON
DEFAULT
    DOUT OT#(3) ON
ENDSWITCH
```

### 16.2 WHILE-ENDWHILE

Repeats a block while a condition is true:

```
WHILE (IN#(1)=ON AND B000=0)
    DOUT OT#(1) ON
    INC B000
ENDWHILE
```

### 16.3 FOR-NEXT

Repeats a block a specific number of times:

```
FOR I000=1 TO 10 STEP 2
    DOUT OT#(I000) ON
NEXT I000
```

### 16.4 IFTHEN-ELSEIF-ELSE-ENDIF

Conditionally executes code blocks:

```
IFTHEN (IN#(1)=ON)
    DOUT OT#(1) ON
ELSEIF (IN#(2)=ON)
    DOUT OT#(2) ON
ELSE
    DOUT OT#(3) ON
ENDIF
```

### Important Notes on Structured Instructions:
- Nesting level can be set with parameter S2C694 (default max: 10)
- Indentation shows nesting level (controlled by parameter S2C693)
- End instructions (ENDIF, ENDSWITCH, etc.) are auto-registered with their corresponding start instructions
- Deleting a structural element may delete multiple lines of code

## 17. SETTM/LATESTJOB Instructions

### SETTM

Controls timer measurement functions:

```
SETTM TM000 TSTART   // Start measuring
SETTM TM000 TSTOP    // Stop measuring
SETTM TM000 TRESET   // Reset timer
SETTM TM000 1000     // Set time and start measuring
```

### LATESTJOB

Reflects job edits made during playback:

```
LATESTJOB
```

## 18. Advanced Control Instructions (Optional Functions)

### 18.1 ADVINIT/ADVSTOP

Control variable data timing in multi-system environments:

```
ADVINIT
ADVSTOP
```

### 18.2 GETARG Instruction

Receives arguments in a called job:

```
GETARG LB000 IARG#(1)   // Get first argument
```

### Parameters:
- Local variable to receive the argument
- IARG#(n): Argument number (1-8)

### 18.3 SETFILE/GETFILE Instructions

Read and modify condition files:

```
SETFILE WEV#(1) (5) 3500    // Change weaving amplitude setting
GETFILE D000 WEV#(1) (6)    // Get vertical distance value
```

### 18.4 SETREG/GETREG Instructions

Set and get register values:

```
SETREG MREG#(5) I000        // Save I000 to register 5
GETREG I000 MREG#(5)        // Get register 5 value into I000
```

### 18.5 GETPRM/SETPRM Instructions

Access and modify system parameters (macro jobs only):

```
GETPRM D000 KIND=4 PRMNO=800 GRPNO=1  // Get parameter
SETPRM KIND=5 PRMNO=67 D000          // Set parameter
```

These control instructions provide the foundation for creating complex, flexible, and modular robot programs in INFORM.
