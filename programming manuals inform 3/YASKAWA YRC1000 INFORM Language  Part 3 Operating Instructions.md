# YASKAWA YRC1000 INFORM Language - Part 3: Operating Instructions

Operating instructions manipulate variables and perform calculations in INFORM programs. They form the computational foundation of the language.

## 1. CLEAR Instruction

The CLEAR instruction initializes variables to zero.

### Syntax:
```
CLEAR <Data 1> <Data 2>
```

### Examples:
```
CLEAR B003 10      // Clear B003 through B012 (10 variables)
CLEAR D010 ALL     // Clear all D variables starting from D010
CLEAR STACK        // Clear all job call stacks
```

### Parameters:

**Data 1** (what to clear):
- B/LB/B[]/LB[] variables
- I/LI/I[]/LI[] variables
- D/LD/D[]/LD[] variables
- R/LR/R[]/LR[] variables
- STACK (job call stack)
- QUE (job queue)
- MPF (memo play file)
- System variables ($B, $I, $D, $R)
- $ERRNO (error status)

**Data 2** (how many to clear):
- Byte constant (number of variables to clear)
- ALL (clear all variables of the type)

### Notes:
- When clearing the job call stack, all return positions are forgotten
- Useful for initializing variables at the start of a program

## 2. INC/DEC Instructions

These instructions increment or decrement variables by 1.

### Syntax:
```
INC <variable>
DEC <variable>
```

### Examples:
```
INC B000           // Add 1 to B000
DEC D005           // Subtract 1 from D005
```

### Valid Variables:
- B/LB/B[]/LB[] variables
- I/LI/I[]/LI[] variables
- D/LD/D[]/LD[] variables

### Notes:
- Simplified alternative to ADD/SUB for incrementing/decrementing
- Commonly used in loops and counters

## 3. SET Instruction

The SET instruction assigns values to variables.

### Syntax:
```
SET <Data 1> <Data 2>
```

### Examples:
```
SET B000 0                           // Set B000 to 0
SET P000 P001                        // Copy P001 to P000
SET OT#(1) LOGICEXP(IN#(1)=ON AND IN#(2)=ON)  // Set output based on logic
SET FL0010 LOGICEXP(B000=1 OR I000=1)         // Set flag based on logic
SET B000 (B001 + B002) / B003                  // Set using expression
```

### Data 1 (destination):
- Any variable type (B, I, D, R, S, P, BP, EX, etc.)
- OT# (output signals)
- OG#/OGU# (output groups)
- FL (flags)

### Data 2 (source):
- Constants
- Variables of compatible types
- Expressions (in STANDARD or EXPANDED instruction sets)
- LOGICEXP conditions

### Notes:
- The most fundamental assignment instruction
- Can perform type conversion when appropriate
- With EXPANDED instruction set, can use expressions with parentheses and operators (+, -, *, /)
- LOGICEXP allows complex logical conditions to set binary outputs

## 4. Arithmetic Instructions (ADD, SUB, MUL, DIV)

These instructions perform basic arithmetic operations.

### Syntax:
```
ADD <Data 1> <Data 2>
SUB <Data 1> <Data 2>
MUL <Data 1> <Data 2>
DIV <Data 1> <Data 2>
```

### Examples:
```
ADD B000 10        // Add 10 to B000
SUB I000 I001      // Subtract I001 from I000
MUL D000 2         // Multiply D000 by 2
DIV P000 (3) D000  // Divide Z-axis of P000 by D000
```

### Data 1 (target):
- B/LB/B[]/LB[] variables
- I/LI/I[]/LI[] variables
- D/LD/D[]/LD[] variables
- R/LR/R[]/LR[] variables
- P/LP/P[]/LP[] variables
- BP/LBP/BP[]/LBP[] variables
- EX/LEX/EX[]/LEX[] variables

### Data 2 (operand):
- Constants of appropriate type
- Variables of compatible types
- For position variables, specific elements can be targeted: (1), (2), etc.

### Notes for Position Variables:
- Element numbers for robot position variables:
  - Pulse type: (1) to (8) for axis data
  - XYZ type: (1)=X, (2)=Y, (3)=Z, (4)=Rx, (5)=Ry, (6)=Rz, (7)=Re
- For MUL/DIV of position variables, specific elements must be targeted

## 5. Conversion Instructions

### 5.1 CNVRT Instruction

Converts position data between coordinate systems.

#### Syntax:
```
CNVRT <Data 1> <Data 2> <Coordinate system>
```

#### Examples:
```
CNVRT PX000 PX001 BF       // Convert to base coordinates
```

#### Parameters:
- Data 1: PX/LPX variable (destination)
- Data 2: PX/LPX variable (source)
- Coordinate system: BF (base), RF (robot), TF (tool), UF# (user), MTF (master tool)
- Optional: TL# (tool number)

### 5.2 MFRAME Instruction

Generates user coordinates from position data.

#### Syntax:
```
MFRAME UF#(1) PX000 PX001 PX002         // Method 1: From three points
MFRAME UF#(1) P000 BF                   // Method 2: From one point
```

#### Methods:
1. From three points defining origin, X-axis, XY-plane
2. From one point specifying the position directly

## 6. Logical Instructions (AND, OR, NOT, XOR)

Perform bitwise logical operations on byte variables.

### Syntax:
```
AND <Data 1> <Data 2>
OR <Data 1> <Data 2>
NOT <Data 1> <Data 2>
XOR <Data 1> <Data 2>
```

### Examples:
```
AND B000 B010      // Logical AND of B000 and B010
OR B000 10         // Logical OR of B000 and 10
NOT B000 B010      // Logical NOT of B010 into B000
XOR B000 1         // Logical XOR of B000 and 1
```

### Parameters:
- Data 1: B/LB/B[]/LB[] variable (destination)
- Data 2: B/LB/B[]/LB[] variable or byte constant (operand)

### Operations:
- AND: Bit-by-bit logical AND (1 AND 1 = 1, others = 0)
- OR: Bit-by-bit logical OR (0 OR 0 = 0, others = 1)
- NOT: Bit-by-bit logical negation (0→1, 1→0)
- XOR: Bit-by-bit exclusive OR (same bits = 0, different bits = 1)

## 7. Position Data Instructions

### 7.1 SETE Instruction

Sets elements of position variables.

#### Syntax:
```
SETE <Data 1> <Data 2>
```

#### Examples:
```
SETE P000 (3) 2000        // Set Z-axis of P000 to 2000
SETE P000 TL#(1)          // Set tool number of P000 to 1
```

#### Parameters:
- Data 1: P/BP/EX variable (target)
- Element selector: (n) for coordinate component or TL# for tool number
- Data 2: D variable or constant (new value)

### 7.2 GETE Instruction

Retrieves elements of position variables.

#### Syntax:
```
GETE <Data 1> <Data 2> (Element number)
```

#### Examples:
```
GETE D000 P000 (3)       // Get Z-axis value from P000 into D000
```

#### Parameters:
- Data 1: D variable (destination)
- Data 2: P/BP/EX variable (source)
- Element number: Component to extract (1-255)

### 7.3 GETPOS Instruction

Retrieves position data from a specific step in the job.

#### Syntax:
```
GETPOS <Data 1> <Data 2>
```

#### Examples:
```
GETPOS PX000 STEP# (1)   // Get position data from step 1
```

#### Parameters:
- Data 1: PX variable (destination)
- Data 2: STEP# step number (1-999)

## 8. Mathematical Functions

### 8.1 SQRT Instruction

Calculates the square root.

#### Syntax:
```
SQRT <Data 1> <Data 2>
```

#### Examples:
```
SQRT R000 2              // Calculate √2 into R000
```

#### Parameters:
- Data 1: R/LR/R[]/LR[] variable (result)
- Data 2: R variable or real constant (operand)

### 8.2 SIN/COS Instructions

Calculates sine and cosine functions.

#### Syntax:
```
SIN <Data 1> <Data 2>
COS <Data 1> <Data 2>
```

#### Examples:
```
SIN R000 60             // Calculate sin(60°) into R000
COS R000 60             // Calculate cos(60°) into R000
```

#### Parameters:
- Data 1: R/LR/R[]/LR[] variable (result)
- Data 2: R variable or real constant (angle in degrees)

### 8.3 ATAN Instruction

Calculates the arc tangent.

#### Syntax:
```
ATAN <Data 1> <Data 2>
```

#### Examples:
```
ATAN R000 60            // Calculate arctan(60) into R000
```

#### Parameters:
- Data 1: R/LR/R[]/LR[] variable (result)
- Data 2: R variable or real constant (operand)

### 8.4 Matrix Operations

#### MULMAT Instruction
Performs matrix multiplication of position data.

```
MULMAT <Data 1> <Data 2> <Data 3>
```

#### INVMAT Instruction
Calculates the inverse matrix of position data.

```
INVMAT <Data 1> <Data 2>
```

#### Usage:
These instructions are useful for three-dimensional shift calculations.

## 9. String Handling Instructions

### 9.1 VAL Instruction

Converts string to numeric value.

#### Syntax:
```
VAL <Data 1> <Data 2> [RADIX=n]
```

#### Examples:
```
VAL B000 "123"          // Convert "123" to numeric 123 in B000
VAL B000 "111" RADIX=2  // Convert binary "111" to 7 in B000
```

#### Parameters:
- Data 1: B/I/D/R variable (result)
- Data 2: String or S variable
- Optional RADIX: Base for conversion (2=binary, 8=octal, 10=decimal, 16=hex)

### 9.2 VAL2STR Instruction

Converts numeric value to string.

#### Syntax:
```
VAL2STR <Data 1> <Data 2>
```

#### Examples:
```
VAL2STR S000 B000       // Convert B000 value to string in S000
```

#### Parameters:
- Data 1: S variable (result)
- Data 2: B/I/D/R variable (numeric value)

### 9.3 ASC Instruction

Gets ASCII code of the first character.

#### Syntax:
```
ASC <Data 1> <Data 2>
```

#### Examples:
```
ASC B000 "ABC"          // Get ASCII code of 'A' (65) into B000
```

#### Parameters:
- Data 1: B variable (result)
- Data 2: String or S variable

### 9.4 CHR$ Instruction

Gets character from ASCII code.

#### Syntax:
```
CHR$ <Data 1> <Data 2>
```

#### Examples:
```
CHR$ S000 65            // Get character 'A' from code 65 into S000
```

#### Parameters:
- Data 1: S variable (result)
- Data 2: B variable or byte constant (ASCII code)

### 9.5 MID$ Instruction

Extracts substring from string.

#### Syntax:
```
MID$ <Data 1> <Data 2> <Data 3> <Data 4>
```

#### Examples:
```
MID$ S000 "123ABC456" 4 3  // Extract "ABC" into S000
```

#### Parameters:
- Data 1: S variable (result)
- Data 2: String or S variable (source)
- Data 3: Starting position (1-based)
- Data 4: Length to extract

### 9.6 LEN Instruction

Gets string length.

#### Syntax:
```
LEN <Data 1> <Data 2>
```

#### Examples:
```
LEN B000 "ABCDEF"       // Get length 6 into B000
```

#### Parameters:
- Data 1: B/I/D variable (result)
- Data 2: String or S variable

### 9.7 CAT$ Instruction

Concatenates strings.

#### Syntax:
```
CAT$ <Data 1> <Data 2> <Data 3>
```

#### Examples:
```
CAT$ S000 "ABC" "DEF"   // Combine to get "ABCDEF" in S000
```

#### Parameters:
- Data 1: S variable (result)
- Data 2: String or S variable (first string)
- Data 3: String or S variable (second string)

### 9.8 STRSTR Instruction

Finds the position of a substring.

#### Syntax:
```
STRSTR <Data 1> <Data 2> <Data 3>
```

#### Examples:
```
STRSTR B000 S000 "DEF"  // Find position of "DEF" in S000, store in B000
```

#### Parameters:
- Data 1: B variable (result - position found, 0 if not found)
- Data 2: S variable (string to search in)
- Data 3: String or S variable (substring to find)

## 10. System Data Access

### 10.1 GETS Instruction

Retrieves system variable data.

#### Syntax:
```
GETS <Data 1> <Data 2>
```

#### Examples:
```
GETS B000 $B002         // Get SRCH result into B000
GETS PX000 $PX000       // Get current position into PX000
GETS I000 $RV           // Get return value from previous job call
```

#### Key System Variables:
- $B001: Execution series number
- $B002: SRCH detection status
- $PX000: Current pulse position
- $PX001: Current XYZ position
- $PX002: Position detected by SRCH (pulse)
- $PX003: Position detected by SRCH (XYZ)
- $RV: Return value from job call

#### Parameters:
- Data 1: Variable to receive value
- Data 2: System variable or $RV (return value)

### 10.2 GETNAME Instruction

Gets the name of a variable.

#### Syntax:
```
GETNAME <Data 1> <Data 2>
```

#### Examples:
```
GETNAME S000 B000       // Get name of B000 into S000
```

#### Parameters:
- Data 1: S variable (result)
- Data 2: Variable, I/O signal, etc.

These operating instructions provide the computational foundation for INFORM programming, allowing manipulation of data, mathematical calculations, string handling, and system interaction.
