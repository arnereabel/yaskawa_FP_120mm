# Changelog

All notable changes to this project will be documented in this file. (format guidelines @ bottom)


## [Unreleased]

### Added
- New feature description.

### Changed
- Update to existing functionality.
- set the pose for the `MULTILAYER-TESTAFLOOP-BOVEN.JBI` so it only uses the xyz from the points from UF.
- create 4 points at start and end for both onder en boven layers to have a set start for run on, run off plates

### Fixed
- Bug fix description (mention issue number if applicable, e.g., #123).

### Removed
- Deprecated `old_function()` which is replaced by `new_module.better_function()`.

---
## [1.0.3](https://github.com/arnereabel/yaskawa_FP_120mm/compare/v1.0.2...v1.0.3) - 21-04-2025
### Changed
-   Adjusted Z coordinate by -150mm for constructed weld start/end points (P003, P004) in `MULTILAYER-TESTAFLOOP-ONDER.JBI`,
    now the points from the userframe will be used to set the start values for `MULTILAYER-TESTAFLOOP-ONDER.JBI` and `MULTILAYER-TESTAFLOOP-BOVEN.JBI`,


## [1.0.2](https://github.com/arnereabel/yaskawa_FP_120mm/compare/v1.0.1...v1.0.2) - 21-04-2025
### Changed
-   Before the welding sequence, the job now constructs two temporary position variables, P003 and P004.
    P003 (weld start) is created by combining the X, Y, Z coordinates from the global variable P001 (which holds the origin point captured in TESTUF-ARNE.JBI) with the Rx, Ry, Rz orientation from the original C00003 point defined within MULTILAYER-TESTAFLOOP-ONDER.JBI.
-   004 (weld end) is created similarly, combining the X, Y, Z from the global variable P002 (the X-axis point
    from TESTUF-ARNE.JBI) with the Rx, Ry, Rz orientation from the original C00004 point.
-   The welding moves now use these constructed points: MOVL P003 T0001 V=83.3 PL=0 for the start 
    and MOVL P004 T0002 V=5.8  for the end.
    This ensures that the weld path in the lower layer job uses the TCP coordinates captured during the user frame definition, while maintaining the specific torch orientation defined for that layer.

---

## [1.0.1](https://github.com/arnereabel/yaskawa_FP_120mm/compare/v1.0.0...v1.0.1) - 20-04-2025
### Changed
-   TESTUF-ARNE.JBI now saves the positions captured by GETPOS PX124 STEP#(1) and GETPOS PX125 STEP#(2) into 
    global Position Variables P001 and P002.
-   MULTILAYER-TESTAFLOOP-BOVEN.JBI now uses P001 for the weld start move (MOVL P001 T0001 V=83.3 PL=0) and P002 for 
    the weld end move (MOVL P002 T0002 V=5.0), replacing the previously used C00003 and C00004.
-   This change ensures that the weld path in the upper layer job directly uses the coordinates captured during the
    user frame definition process in TESTUF-ARNE.JBI.

---

## [1.0.0](https://github.com/arnereabel/yaskawa_FP_120mm/releases/tag/v1.0.0) - 19-04-2025

### Added
- Initial release of the project.
- Core features: welding of a multylayer weld.

### Changed
- N/A (Initial Release)

### Fixed
- N/A (Initial Release)

### Removed
- N/A (Initial Release)







/////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
format and guidelines

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- New feature description.

### Changed
- Update to existing functionality.

### Fixed
- Bug fix description (mention issue number if applicable, e.g., #123).

### Removed
- Deprecated `old_function()` which is replaced by `new_module.better_function()`.

---

## [1.1.0] - 21-04-2025

### Added
- Support for importing CSV files.
- User preference setting for theme (dark/light).

### Changed
- Improved error handling during API calls.
- Refactored the authentication module for clarity.

### Fixed
- Resolved issue where login button was sometimes unresponsive (#98).
- Corrected calculation error in the reporting module.

### Removed
- Deprecated `old_function()` which is replaced by `new_module.better_function()`.

---

## [1.0.1] - 20-04-2025

### Fixed
- Security patch for potential XSS vulnerability in user profiles (#75).
- Minor typo in the documentation homepage.

---

## [1.0.0] - 19-04-2025

### Added
- Initial release of the project.
- Core features: welding of a multylayer weld.

### Changed
- N/A (Initial Release)

### Fixed
- N/A (Initial Release)

### Removed
- N/A (Initial Release)


Header: Briefly explain the file's purpose and mention the standards being followed (Keep a Changelog, SemVer).
[Unreleased]:
This is crucial. It's a rolling list of changes for the next version.
When you're ready to release, you:
Change [Unreleased] to the new version number and add the date (e.g., ## [1.2.0] - 2025-05-01).
Add a new ## [Unreleased] section above it, ready for the next set of changes.
Version Sections ([MAJOR.MINOR.PATCH] - YYYY-MM-DD):
Each released version gets its own section.
The version number should link to a tag or release page in your version control system (like GitHub or GitLab) if possible.
Change Categories (within each version): Use these standard categories:
Added: For new features.
Changed: For changes in existing functionality.
Deprecated: For features that will be removed in upcoming releases.
Removed: For features removed in1 this version.   
1.
data.safetycli.com
data.safetycli.com
Fixed: For any bug fixes.
Security: For changes addressing vulnerabilities.
You might only use a subset of these in any given release.
5. Writing Change Descriptions:

Be Clear and Concise: Explain the change from a user's or developer's perspective.
Use Imperative Mood: Start lines with verbs like "Add", "Fix", "Change", "Remove" (e.g., "Fix calculation error..." instead of "Fixed calculation error...").
Link to Issues/Pull Requests: If applicable, include links or references (like #123) to your issue tracker or PRs for more context.
Highlight Breaking Changes: If a change breaks backward compatibility, make it very prominent, often by adding a **BREAKING CHANGE:** prefix or a dedicated Breaking section.
