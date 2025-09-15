

Project Description :

This project involves a Yaskawa robot program designed to perform a multi-layer weld on an X-seam configuration for a 120mm thick plate (presumably 60mm bevel with 40° angle on each side). 
Specifically welding in PA and PE alternately to fill the groove with a 12.50 OK wire from esab for both positions.  
The program utilizes several JBI files to manage different aspects of the process, including user frame definition, master control sequencing, welding operations for top and bottom sections, and transitions between these sections.
Core task involves automating the welding process for this specific joint geometry.

Core Goals :

Automate the multi-layer welding process for the specified 120mm X-seam.
Ensure accurate weld deposition by defining a precise user frame and potentially teaching start/end points.
Achieve a complete and structurally sound weld covering both the top ("BOVEN") and bottom ("ONDER") sections of the seam.
Manage robot movements and transitions between welding areas safely and efficiently.
Create a modular and maintainable program structure using separate JBI files.

Key Requirements :

User Frame Definition: A job (TESTUF-ARNE.JBI) to accurately define the coordinate system relative to the workpiece.
Master Control: A main job (MASTER-START-ML.JBI) to orchestrate the overall sequence of operations.
Sectional Welding Jobs: Separate jobs (MULTILAYER-TESTAFLOOP-BOVEN.JBI, MULTILAYER-TESTAFLOOP-ONDER.JBI) to handle the welding logic for the top and bottom parts of the X-seam.
Point Teaching/Detection: Functionality within the welding jobs to determine and potentially save the start and end points for each weld pass.
Transition Jobs: Dedicated jobs (VAN-BOVEN-NAAR-ONDER.JBI, VAN-ONDER-NAAR-BOVEN.JBI) to manage the robot's movement between the upper and lower welding zones.
Multi-Layer Capability: Logic to handle multiple weld layers, potentially involving macros for shifting (SHIFT-R1.JBI) or weaving (WEAVING.JBI).
![20250227_110620](https://github.com/user-attachments/assets/3ae08a88-4795-4a28-a985-fb99805f5fd1 alt="alternate text" width="300")
