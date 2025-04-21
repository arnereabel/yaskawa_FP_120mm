# YASKAWA YRC1000 INFORM Language - Part 4: Move Instructions

Move instructions control the robot's motion, defining paths, speeds, and positioning accuracy. They form the foundation of robot programming for physical movement.

## 1. MOVJ Instruction (Joint Movement)

The MOVJ instruction moves the robot to a taught position using joint interpolation.

### Syntax:
```
MOVJ [position] VJ=50.00
```

### Examples:
```
MOVJ VJ=50.00               // Move to taught position at 50% joint speed
MOVJ P000 VJ=50.00          // Move to position in P000 at 50% joint speed
MOVJ P000 BP000 VJ=25.00    // Move robot and base axes
```

### Key Parameters:

#### Target Position:
- Robot teaching position file (implicit if not specified)
- P/LP/P[]/LP[] variable (robot axis)
- BP/LBP/BP[]/LBP[] variable (base axis)
- EX/LEX/EX[]/LEX[] variable (station axis)
- T point variable (optional point variable reference)

#### Speed Control:
- VJ=joint speed (0.01% to 100.00% of max speed)

#### Position Control:
- PL=position level (0-8, approach accuracy)
- SPDL=speed level (servo float function)

#### Rotation Control:
- MT=T axis rotation count (-32768 to 32767)
- MTE=station axis rotation count (-32768 to 32767)

#### Timing Control:
- NWAIT (perform next instruction simultaneously)
- ENWAIT (execute next instruction before reaching position)
- UNTIL (conditionally terminate motion)

#### Motion Adjustment:
- ACC=acceleration adjustment ratio (20% to 100%)
- DEC=deceleration adjustment ratio (20% to 100%)
- TRAP (error handling for limit conditions)

### Notes:
- Joint interpolation moves each axis independently at constant speed ratio
- Path is not necessarily straight; use when path doesn't matter
- Fastest motion type, but less predictable path

## 2. MOVL Instruction (Linear Movement)

The MOVL instruction moves the robot to a taught position using linear interpolation.

### Syntax:
```
MOVL [position] V=138
```

### Examples:
```
MOVL V=138                  // Move to taught position at 138 mm/s
MOVL P000 V=138             // Move to position in P000 at 138 mm/s
MOVL P000 BP000 VR=30.0     // Move with position angular speed control
```

### Key Parameters:

#### Target Position:
- Same position options as MOVJ

#### Speed Control:
- V=tool center point speed (mm/s)
- VR=position angular speed (deg/s)
- VE=external axis speed (0.01% to 100.00%)
- VMAX=maximum speed ratio (50% to 100%)

#### Path Control:
- PL=position level (0-8)
- CR=corner radius (0.1mm to 6553.5mm)
- PD=position distance (inward turning point)

#### Search Control (Optional Functions):
- SRCH (detect starting point)
- ASRCH (detect input signal voltage)
- HPSRCH (detect zero-point position)
- NSRCH (detect position without stopping)

### Notes:
- Linear path with constant speed at tool center point
- Slower than MOVJ but follows precise straight line
- Used when path matters (e.g., straight approaches)

## 3. MOVC Instruction (Circular Movement)

The MOVC instruction moves the robot along a circular arc defined by three points.

### Syntax:
```
MOVC [position] V=138
```

### Examples:
```
MOVL V=138       // Start point
MOVC V=138       // Point on arc
MOVC V=138       // End point
MOVC FPT V=138   // Fixed point marker
```

### Key Parameters:
- Same position and speed options as MOVL
- FPT: Marks a fixed point (end/midpoint) of an arc segment
- COORD=Arc attitude control (0=parallel to robot installation, 1=non-parallel)

### Notes:
- Requires three consecutive MOVC instructions in the same job
- First three points define first arc
- Subsequent points create continuous arcs
- Use FPT to mark fixed points where arc curvature changes

## 4. MOVS Instruction (Spline Movement)

The MOVS instruction moves the robot through taught points with smooth spline interpolation.

### Syntax:
```
MOVS [position] V=138
```

### Examples:
```
MOVL V=138       // Start point
MOVS V=138       // Spline point 1
MOVS V=138       // Spline point 2
MOVS V=138       // Spline point 3
MOVL V=138       // End point
```

### Key Parameters:
- Same position and speed options as MOVL/MOVC

### Notes:
- Creates smooth curved paths through multiple points
- First and last points are not on the spline
- Middle points are passed with smooth transition
- Used for complex curved paths

## 5. IMOV Instruction (Incremental Movement)

The IMOV instruction moves the robot by a specified increment from current position.

### Syntax:
```
IMOV [position] V=138 RF
```

### Examples:
```
IMOV P000 V=138 RF          // Move by increment in P000, robot coordinates
IMOV P000 V=138 TF          // Move by increment in P000, tool coordinates
```

### Key Parameters:
- Position variables specifying the increment amount
- Coordinate system specification:
  - BF: Base coordinates
  - RF: Robot coordinates
  - TF: Tool coordinates
  - UF#: User coordinates
  - MTF: Master tool coordinates

### Notes:
- Used for small adjustments relative to current position
- Useful for search patterns, offset movements
- Coordinate system determines reference frame for increment

## 6. SPEED Instruction (Speed Setting)

The SPEED instruction sets default playback speed for subsequent move instructions.

### Syntax:
```
SPEED VJ=50.00 V=276 VR=30.0
```

### Examples:
```
SPEED VJ=50.00 V=276        // Set joint and linear speeds
MOVJ                        // Uses VJ=50.00
MOVL                        // Uses V=276
```

### Key Parameters:
- VJ=joint speed (0.01% to 100.00%)
- V=tool center point speed (mm/s)
- VR=position angular speed (deg/s)
- VE=external axis speed (0.01% to 100.00%)

### Notes:
- Sets default speeds for subsequent move instructions
- Remains in effect until another SPEED instruction
- Individual move instructions can override with own speed values

## 7. REFP Instruction (Reference Point)

The REFP instruction defines reference points, typically for welding operations.

### Syntax:
```
REFP [reference point number]
```

### Examples:
```
REFP 1                      // Define reference point 1
REFP 1 P000                 // Define using position in P000
```

### Key Parameters:
- Reference point number (1-8)
- RBn/STn: Robot or station to which reference applies
- Position variables (if using variable instead of taught position)

### Notes:
- Used to define reference points for operations
- In welding, REFP1 typically defines wall direction
- In welding, REFP2 typically defines horizontal wall direction

## 8. Common Move Instruction Parameters

### 8.1 Position Levels (PL=)

Position levels control approach accuracy:

| Level | Description |
|-------|-------------|
| 0 | Exact positioning (complete stop) |
| 1-8 | Progressively larger approach zones |

Position zones are defined by parameters S1CxG33-S1CxG40.

### 8.2 Speed Control

Multiple speed control options:
- VJ=joint speed (MOVJ, percentage of maximum)
- V=linear speed (MOVL/MOVC/MOVS, mm/s)
- VR=rotational speed (degrees/s)
- VE=external axis speed (percentage)
- VMAX=maximum speed ratio

### 8.3 Acceleration/Deceleration Control

- ACC=acceleration adjustment (20% to 100%)
- DEC=deceleration adjustment (20% to 100%)

Reduces acceleration/deceleration to protect payload.

### 8.4 Timing Control

- NWAIT: Simultaneously execute next instruction
- ENWAIT: Execute next instruction before reaching position
- UNTIL: Conditionally terminate motion when input condition is met
- T= or DIS=: Time or distance before executing next instruction (with ENWAIT)

### 8.5 Error Handling

- TRAP: Jump to specified label if limit exceeded
- Search instructions (SRCH, etc.): Detect positions during motion

### 8.6 Coordinate Systems

All move instructions operate in specific coordinate systems:

| System | Description |
|--------|-------------|
| Base (BF) | Fixed to robot base |
| Robot (RF) | Fixed to robot (S-axis) |
| Tool (TF) | Fixed to tool flange |
| User (UF#) | User-defined coordinates |
| Master Tool (MTF) | Relative to master manipulator |

## 9. Multi-Robot and Advanced Motion Control

### 9.1 Coordinated Robot Control

For multi-robot systems, additional prefixes specify which robot executes motion:

```
+MOVJ      // Master side motion
+MOVL      // Master side motion
+MOVC      // Master side motion
+MOVS      // Master side motion
+IMOV      // Master side incremental motion
```

### 9.2 3D Shift Control

With the optional multi-layer welding function, 3D shift operations are supported:

```
+SFTON3D P000      // 3D shift by amount in P000
```

Shifts the teaching position in a coordinated translation/rotation.

## 10. Path Planning Considerations

### 10.1 Corner Radius (CR=)

For MOVL, allows rounding corners with specified radius:

```
MOVL V=138 CR=50.0      // 50mm corner radius
```

Creates a smooth transition between linear segments.

### 10.2 Position Distance (PD=)

Controls inward turning point for MOVL:

```
MOVL V=138 PD=100.0     // Start inward turning 100mm from end
```

Starts decelerating at specified distance from end point.

### 10.3 Circular Motion Planning

MOVC requires careful planning:
- Three consecutive points define an arc
- Use FPT to mark fixed points
- Use COORD= for proper orientation control

### 10.4 Spline Motion Planning

MOVS forms curves through points:
- First and last points are not on curve
- Middle points lie on curve
- Creates smooth transitions
- Can have many points in sequence

## 11. Position Variables and Teaching

### 11.1 Teaching Methods

Positions can be specified via:
- Direct teaching (stored in teaching position file)
- Position variables (P, BP, EX)
- Point variables (T)

### 11.2 Position Variable Structure

Robot position (P) contains:
- XYZ coordinates
- Orientation angles (Rx, Ry, Rz)
- Additional configuration data
- Tool information

Base position (BP) contains:
- Base axis values

Station position (EX) contains:
- Station axis values

These move instructions, combined with proper parameters, enable precise control of robot motion for complex applications. The choice of interpolation method, speed control, and positioning accuracy depends on the specific requirements of the application.
