# YASKAWA YRC1000 INFORM Language - Part 6: Arc Welding Instructions

INFORM provides comprehensive support for arc welding applications with specialized instructions. These instructions control welding parameters, weaving patterns, and process timing for optimal weld quality.

## 1. Basic Arc Welding Instructions

### 1.1 ARCON Instruction (Arc On)

The ARCON instruction outputs the welding start command.

#### Syntax:
```
ARCON [condition file/parameters]
```

#### Examples:
```
ARCON ASF#(1)                      // Start welding with condition file 1
ARCON AC=220 AVP=100 T=0.50        // Start with specific current/voltage
```

#### Parameters:

**Condition Method:**
- ASF#(n): Arc start condition file number (1-1000)

**Direct Parameter Method:**
- AC=current: Welding current output value (1-999A)
- AV=voltage: Arc voltage output value (0.1-50.0V)
- AVP=ratio: Ratio to proper voltage output (50-150%)
- T=time: Timer value at welding start (0.01-655.35s)
- V=speed: Welding speed while welding (cm/min)

**Error Recovery:**
- RETRY: Automatic retry on arc generation failure
- REPLAY: Return to previous step on retry

#### Multiple Power Sources:
For systems with multiple welding power sources:
```
ARCON WELD1 ASF#(1)               // Start welding with power source 1
```

#### Notes:
- ASF method uses pre-configured welding parameters
- Direct parameter method allows runtime specification
- RETRY function recovers from arc generation failures automatically
- Timer parameter controls initial welding time before proceeding

### 1.2 ARCOF Instruction (Arc Off)

The ARCOF instruction outputs the welding end command.

#### Syntax:
```
ARCOF [condition file/parameters]
```

#### Examples:
```
ARCOF AEF#(1)                     // End welding with condition file 1
ARCOF AC=160 AVP=90 T=0.50        // End with specific settings
```

#### Parameters:

**Condition Method:**
- AEF#(n): Arc end condition file number (1-1000)

**Direct Parameter Method:**
- AC=current: Welding current output value (1-999A)
- AV=voltage: Arc voltage output value (0.1-50.0V)
- AVP=ratio: Ratio to proper voltage output (50-150%)
- T=time: Timer value at welding end (0.01-655.35s)

**Wire Sticking Prevention:**
- ANTSTK: Automatic sticking release function

#### Multiple Power Sources:
For systems with multiple welding power sources:
```
ARCOF WELD1 AEF#(1)               // End welding with power source 1
```

#### Notes:
- AEF method uses pre-configured end parameters
- ANTSTK automatically releases stuck wire
- End parameters often differ from start parameters for optimal crater filling

### 1.3 VWELD/AWELD Instructions

These instructions directly set welding voltage or current by command value.

#### Syntax:
```
VWELD [voltage command value]
AWELD [current command value]
```

#### Examples:
```
VWELD 6.0                         // Set voltage command to 6.0V
AWELD 6.0                         // Set current command to 6.0V
```

#### Notes:
- Command values (-14.00V to +14.00V) are not the actual welding values
- Actual welding values depend on power source characteristics
- Typically used for fine adjustment during welding

### 1.4 ARCSET Instruction

The ARCSET instruction changes welding parameters during welding.

#### Syntax:
```
ARCSET [parameters]
```

#### Examples:
```
ARCSET AC=250                      // Change current during welding
ARCSET ASF#(2) ACOND=1             // Change to main condition in file 2
```

#### Parameters:
- Same parameter options as ARCON
- ACOND=0/1: Select start condition (0) or main condition (1)
- AN3/AN4: Aimed values of instruction voltage to analog outputs

#### Notes:
- Used to change welding parameters during a continuous weld
- Enables parameter transitions without stopping arc
- Can switch between condition files or directly set parameters

## 2. Weaving Instructions

Weaving creates oscillating motion patterns during welding to improve penetration and gap bridging.

### 2.1 WVON Instruction (Weaving On)

The WVON instruction starts weaving operation.

#### Syntax:
```
WVON [parameters]
```

#### Examples:
```
WVON WEV#(2)                       // Start weaving with condition file 2
WVON AMP=5.0 FREQ=3.0 ANGL=40.0    // Start with specific settings
```

#### Parameters:

**Condition Method:**
- WEV#(n): Weaving condition file number (1-255)

**Direct Parameter Method:**
- AMP=amplitude: Single amplitude of weaving (0.1-99.9mm)
- FREQ=frequency: Weaving frequency (0.1-5.0Hz)
- ANGL=angle: Weaving angle (0.1-180.0°)
- DIR=direction: Direction of weaving (0=forward, 1=reversed)

#### Multiple Robots:
For systems with multiple robots:
```
WVON RB1 WEV#(2)                   // Start weaving with Robot 1
```

#### Notes:
- Weaving condition files store comprehensive weaving parameters:
  - Mode (TRIANGLE, L-TYPE, ELLIPSE, etc.)
  - Pattern dimensions
  - Timing parameters
- Direct parameter method allows runtime specification
- Weaving operates independently of welding (can be used for other applications)

### 2.2 WVOF Instruction (Weaving Off)

The WVOF instruction ends weaving operation.

#### Syntax:
```
WVOF
```

#### Multiple Robots:
For systems with multiple robots:
```
WVOF RB1                           // End weaving with Robot 1
```

#### Notes:
- Terminates weaving motion
- Often used after ARCOF but can be used independently

### 2.3 Weaving Patterns

The weaving condition files support multiple patterns:

| Pattern | Description |
|---------|-------------|
| SINGLE | Simple straight line |
| TRIANGLE | Triangular oscillation |
| L-TYPE | L-shaped pattern |
| ELLIPSE | Elliptical pattern |

Each pattern supports specific parameters for amplitude, angle, and timing.

## 3. Advanced Welding Control

### 3.1 ARCCTS/ARCCTE Instructions (Slope Up/Down)

These instructions control welding parameters gradually during a move.

#### Syntax:
```
ARCCTS [parameters] DIS=[distance]
ARCCTE [parameters] DIS=[distance]
```

#### Examples:
```
ARCCTS AC=150 AV=16.0 DIS=100.0    // Gradually change parameters in first 100mm
ARCCTE AC=150 AV=16.0 DIS=100.0    // Gradually change parameters in last 100mm
```

#### Parameters:
- AC=current: Target welding current (1-999A)
- AV=voltage: Target arc voltage (0.1-50.0V)
- AVP=ratio: Target ratio to proper voltage (50-150%)
- AN3/AN4: Target analog output voltages
- DIS=distance: Execution section from start/end point (mm)

#### Notes:
- ARCCTS (Arc Control Start): Changes parameters in the starting section
- ARCCTE (Arc Control End): Changes parameters in the ending section
- Used for slope up/down control to improve weld quality
- Particularly useful for aluminum and thin materials
- Parameters change gradually over the specified distance

### 3.2 Welding Condition Files

Arc welding uses several condition files to store parameters:

1. **Arc Start Condition File (ASF#)**
   - Initial conditions for arc start
   - Includes pre-flow time, start current/voltage, etc.

2. **Arc End Condition File (AEF#)**
   - Final conditions for arc end
   - Includes crater fill current/voltage, post-flow time, etc.

3. **Weaving Condition File (WEV#)**
   - Pattern definition and timing
   - Includes pattern type, dimensions, frequencies, etc.

4. **Auxiliary Condition File**
   - Settings for RETRY and ANTSTK functions
   - Error recovery parameters

### 3.3 Enhanced Welding Condition File Function

With the enhanced function, each condition file contains:
- Start condition (used at welding start)
- Main condition (used during main welding)

The ACOND parameter selects which to use:
```
ARCSET ASF#(1) ACOND=0             // Use start condition
ARCSET ASF#(1) ACOND=1             // Use main condition
```

## 4. Typical Welding Program Structure

A typical arc welding program follows this structure:

### 4.1 Basic Structure

```
MOVJ VJ=50.00                      // Approach position
MOVJ VJ=25.00                      // Pre-weld position
MOVL V=220                         // Move to weld start
WVON WEV#(2)                       // Start weaving
ARCON ASF#(1)                      // Start welding
MOVL V=138                         // Weld path
ARCOF AEF#(1)                      // End welding
WVOF                               // End weaving
MOVL V=220                         // Exit position
MOVJ VJ=50.00                      // Return position
```

### 4.2 With Parameter Changes

```
MOVJ VJ=50.00                      // Approach position
MOVL V=220                         // Move to weld start
ARCON AC=200 AVP=100 T=0.30        // Start welding
MOVL V=50                          // Weld path segment 1
ARCSET AC=250                      // Change parameters
MOVL V=65                          // Weld path segment 2
ARCOF                              // End welding
MOVJ VJ=50.00                      // Return position
```

### 4.3 With Slope Control

```
MOVJ VJ=50.00                      // Approach position
MOVL V=220                         // Move to weld start
ARCON AC=200 AVP=100               // Start welding
ARCCTS AC=250 AV=25.0 DIS=50.0     // Slope up current/voltage
MOVL V=138                         // Weld path
ARCCTE AC=180 AV=20.0 DIS=50.0     // Slope down current/voltage
ARCOF                              // End welding
MOVJ VJ=50.00                      // Return position
```

## 5. Reference Points for Welding

Reference points (REFP) are often used to define the welding orientation.

### 5.1 Wall Direction Reference

```
REFP 1                             // Define reference point 1
```

When the wall direction and Z-axis direction are different, REFP1 defines the wall direction for proper weaving orientation.

### 5.2 Approach Direction Reference

```
REFP 2                             // Define reference point 2
```

When the approach point is on the other side of the wall, REFP2 defines the horizontal wall direction.

### 5.3 Usage in Welding Programs

```
MOVL V=138                         // Move to reference position
REFP 1                             // Define wall direction
MOVL V=138                         // Move to weld start
ARCON ASF#(1)                      // Start welding with proper orientation
```

## 6. Arc Welding Best Practices

### 6.1 Parameter Selection

- Use lower current/voltage for thin materials
- Increase parameters gradually for thick materials
- Consider material properties (aluminum requires different settings than steel)
- Use slope up/down for crater control
- Adjust weaving parameters based on gap width and joint type

### 6.2 Motion Planning

- Use linear interpolation (MOVL) for straight welds
- Use circular interpolation (MOVC) for curved welds
- Maintain consistent speed throughout weld
- Consider approach and exit paths for optimal access
- Use REFP instructions for proper orientation

### 6.3 Error Recovery

- Implement RETRY function for automatic arc recovery
- Use ANTSTK for wire stick prevention
- Consider sensor feedback for adaptive welding
- Implement proper error handling with TRAP instruction

### 6.4 Process Optimization

- Use weaving for gap bridging and better penetration
- Implement slope control for material-specific optimization
- Adjust travel speed based on material thickness
- Use multiple weld passes for thick materials
- Consider preheat and interpass temperature requirements

## 7. Related Functionality

### 7.1 Shift Operations for Welding

Shift operations are particularly useful in welding applications:

```
SFTON P000 UF#(1)                  // Apply shift for weld path variation
[welding operations]
SFTOF                              // End shift
```

For multi-layer welding, 3D shifts maintain proper orientation:

```
SFTON3D P000                       // Apply 3D shift for next layer
[welding operations]
SFTOF3D                            // End 3D shift
```

### 7.2 Sensing Integration

For adaptive welding, search instructions are often combined with welding:

```
MOVL V=100 SRCH                    // Search for workpiece
GETS PX002 $PX002                  // Get detected position
[calculate adjustments]
SFTON P000 UF#(1)                  // Apply calculated shift
[welding operations]
```

### 7.3 I/O Integration

Welding often requires coordinated I/O for process control:

```
DOUT OT#(1) ON                     // Turn on shielding gas
TIMER T=1.50                       // Pre-flow time
ARCON ASF#(1)                      // Start welding
[welding operations]
ARCOF AEF#(1)                      // End welding
TIMER T=1.50                       // Post-flow time
DOUT OT#(1) OFF                    // Turn off shielding gas
```

These arc welding instructions provide comprehensive control for welding processes, enabling high-quality, repeatable welds in industrial applications. By combining welding instructions with motion control and shift capabilities, INFORM enables sophisticated welding operations for a wide range of applications.
