# Reviewer Guide

## 1. Closed-Loop Method and Results

Read [the consolidated author-note replacement](docs/paper/author-note-replacements.md). It contains the objective formulation, discrete Frechet calculation, CEID search description, actual fixed-budget stopping rule, full 24-trial error table, supplementary pseudocode, gravimetric validation, patent declaration, licensing statement, and access wording.

Machine-readable supporting records are in [`docs/paper/data`](docs/paper/data):

- `ceid_closed_loop_trace.csv`: sanitized per-trial formulations, Frechet errors, and best-so-far values.
- `target_spectrum.json`: target recipe, calibrated RGB response, and normalized AS7341 spectrum.
- `strategy_comparison_summary.csv`: strategy-level physical results with historical-data caveats.
- `gravimetric_repeatability_measured.csv`: five raw balance readings per nominal volume and calculated statistics.

## 2. Digital-Twin Reference Material

The pseudocode-only references are available for [CEID](docs/pseudocode/CEID.md) and the [Unity digital twin](docs/pseudocode/UNITY_DIGITAL_TWIN.md). They summarize the closed-loop and user-control behaviour without including implementation source code. The corresponding manuscript material remains in [`docs/paper/author-note-replacements.md`](docs/paper/author-note-replacements.md).

## 3. Reproducibility Boundary

The CEID engine and hardware implementation are proprietary and are not included in the public repository. The public records provide the target, constraints, objective definition, complete primary CEID trial trace, measured dispensing-validation data, and pseudocode-only workflow notes required to inspect the reported workflow.

The repository excludes implementation source code for both CEID and Unity, as requested for review. A browser-based WebGL build will be linked here after deployment and independent testing.

## 4. Security

No static participant token is required to inspect the source or data. Any live demonstration must use short-lived tokens minted by a server-side service. Never place a LiveKit API secret or reusable participant token in Unity scenes, browser builds, screenshots, or repository files.
