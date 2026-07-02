# Supporting Data Notes

## CEID Trace

`ceid_closed_loop_trace.csv` contains the 24 physical experiments from the primary closed-loop run. Trials 1-8 are the space-filling initialization; trials 9-24 are model-based selections. Trial 16 reports the corrected confirmatory remeasurement value of `0.014524`; the original exported value was `0.022928774929`.

## Target Spectrum

`target_spectrum.json` records the experimentally captured A1 target recipe and normalized AS7341 spectrum used in the Frechet objective.

## Strategy Comparison

`strategy_comparison_summary.csv` distinguishes new physical experiments from imported historical rows. The resumed language-model-guided condition is not an independent 24-experiment run.

## Gravimetric Validation

`gravimetric_repeatability_measured.csv` contains five experimentally validated balance readings at each nominal volume. Sample standard deviation uses the `n - 1` denominator. Calculated volume assumes a water density of `0.998 mg/uL`; the final Methods section must state the measured water temperature and use the corresponding density.
