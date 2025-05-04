# Product Context

*This file describes the "why" behind the project: the problems it solves, its intended functionality, and the desired user experience.*

## Problem Statement

Manually welding thick, multi-layer X-seams is time-consuming, requires highly skilled welders, and can lead to inconsistencies in weld quality and penetration. Automating this process with a robot aims to improve efficiency, repeatability, and overall weld integrity.

## Intended Functionality

From the operator's perspective:
1.  The operator potentially initiates a setup routine (`TESTUF-ARNE.JBI`) to define the workpiece location (user frame).
2.  The operator starts the main program (`MASTER-START-ML.JBI`).
3.  The robot automatically executes the entire multi-layer welding sequence for the 120mm X-seam, including:
    *   Welding the bottom section (`MULTILAYER-TESTAFLOOP-ONDER.JBI`).
    *   Transitioning to the top section (`VAN-ONDER-NAAR-BOVEN.JBI`).
    *   Welding the top section (`MULTILAYER-TESTAFLOOP-BOVEN.JBI`).
    *   Possibly transitioning back if needed (`VAN-BOVEN-NAAR-ONDER.JBI`).
4.  The process might involve teaching or detecting start/end points for the welds within the respective jobs.

## User Experience Goals

- **Reliability:** The program should consistently produce high-quality welds according to specified parameters.
- **Repeatability:** Each execution should follow the same path and parameters precisely.
- **Efficiency:** Minimize cycle time while maintaining weld quality.
- **Ease of Setup:** The process for defining the user frame and potentially teaching points should be straightforward for the operator.
- **Clarity:** The program structure and flow should be understandable for maintenance and troubleshooting.
