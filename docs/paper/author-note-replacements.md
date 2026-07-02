# Manuscript-Ready Author-Note Replacements

This document consolidates the replacement text for the CEID algorithm, closed-loop results, gravimetric validation, patents, data availability, software and hardware licences, and reviewer access.

## 1. CEID Algorithm and Closed-Loop Optimisation

CEID was implemented as a constrained, closed-loop optimisation engine for autonomous colour formulation. A candidate formulation was represented by

\[
x=(v_R,v_Y,v_B,v_W),
\]

where \(v_R\), \(v_Y\), \(v_B\), and \(v_W\) are discrete transfer units of red, yellow, blue, and water, respectively. Each variable was restricted to an integer value from 1 to 5, each unit represented 100 uL, and the total formulation was constrained by

\[
v_R+v_Y+v_B+v_W \leq 13,
\]

corresponding to a maximum mixture volume of 1,300 uL.

The optimisation target was an experimentally captured reference mixture prepared in well A1 using four red units, three yellow units, four blue units, and two water units. The reference therefore had a total volume of 1,300 uL. Its calibrated RGB response was \(R=18\), \(G=20\), and \(B=11\). The optimisation itself used the normalized eight-channel AS7341 spectrum rather than RGB values alone. The target vector was

\[
S^*=(0.039722, 0.319000, 0.211944, 0.242250, 0.324583, 0.391861, 0.356694, 0.182056)
\]

at wavelengths 415, 445, 480, 515, 555, 590, 630, and 680 nm, respectively.

For every candidate \(x\), the robotic platform prepared the mixture, collected the AS7341 response, normalized each spectral channel, and constructed the measured spectral curve

\[
P_x=\{(\lambda_i,s_i(x))\}_{i=1}^{8}.
\]

The target curve was \(P^*=\{(\lambda_i,s_i^*)\}_{i=1}^{8}\). The scalar objective was the discrete Frechet distance

\[
f(x)=d_F(P_x,P^*),
\]

which was minimized. The implementation evaluated the Euclidean distance between wavelength-intensity points and used the standard discrete Frechet recurrence

\[
c(i,j)=\max\left(d(p_i,q_j),\min\{c(i-1,j),c(i-1,j-1),c(i,j-1)\}\right),
\]

with the conventional boundary conditions. Smaller values therefore indicate closer agreement between the measured and target spectral curves.

The search began with eight space-filling initial formulations. CEID then updated a probabilistic surrogate from all completed experiments and selected 16 additional feasible formulations using a model-based acquisition policy. The acquisition stage used both predicted objective performance and model uncertainty while enforcing the discrete variable bounds and total-volume constraint. Experiments were executed in batches of four, giving a total budget of 24 physical experiments.

The production configuration did not use numerical early stopping: its operational stopping criterion was completion of the fixed 24-experiment budget. For result interpretation, the final incumbent was first discovered at trial 16. The best-so-far distance first fell below 0.03 at trial 14 and below 0.02 at trial 16. The remaining eight experiments did not improve the incumbent, providing an empirical stability check within the allocated budget.

## 2. Quantitative Closed-Loop Results

CEID obtained its final best formulation at trial 16: five red units, one yellow unit, five blue units, and two water units, corresponding to 500, 100, 500, and 200 uL, respectively. A confirmatory remeasurement of this formulation produced the corrected Frechet distance of 0.014524. The result export records the original trial-16 value as 0.022929 and identifies 0.014524 as the corrected remeasurement value. This formulation remained the incumbent through trial 24.

| Trial | Red | Yellow | Blue | Water | Frechet error | Best so far |
| ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| 1 | 4 | 1 | 5 | 2 | 0.032444 | 0.032444 |
| 2 | 1 | 4 | 1 | 4 | 0.036194 | 0.032444 |
| 3 | 2 | 2 | 4 | 1 | 0.118472 | 0.032444 |
| 4 | 3 | 1 | 2 | 4 | 0.039752 | 0.032444 |
| 5 | 2 | 3 | 5 | 1 | 0.082611 | 0.032444 |
| 6 | 1 | 1 | 2 | 5 | 0.133833 | 0.032444 |
| 7 | 5 | 2 | 2 | 1 | 0.098694 | 0.032444 |
| 8 | 1 | 4 | 4 | 4 | 0.087194 | 0.032444 |
| 9 | 4 | 1 | 4 | 3 | 0.035667 | 0.032444 |
| 10 | 1 | 5 | 1 | 4 | 0.134722 | 0.032444 |
| 11 | 1 | 4 | 1 | 2 | 0.141047 | 0.032444 |
| 12 | 2 | 4 | 1 | 5 | 0.091167 | 0.032444 |
| 13 | 3 | 1 | 4 | 3 | 0.063194 | 0.032444 |
| 14 | 4 | 1 | 3 | 2 | 0.024472 | 0.024472 |
| 15 | 4 | 1 | 2 | 4 | 0.042500 | 0.024472 |
| 16 | 5 | 1 | 5 | 2 | 0.014524* | 0.014524 |
| 17 | 5 | 1 | 3 | 2 | 0.069000 | 0.014524 |
| 18 | 5 | 1 | 4 | 3 | 0.036639 | 0.014524 |
| 19 | 5 | 1 | 4 | 2 | 0.092278 | 0.014524 |
| 20 | 5 | 3 | 4 | 1 | 0.067767 | 0.014524 |
| 21 | 3 | 1 | 3 | 2 | 0.075472 | 0.014524 |
| 22 | 4 | 1 | 5 | 3 | 0.048917 | 0.014524 |
| 23 | 3 | 1 | 3 | 3 | 0.055549 | 0.014524 |
| 24 | 4 | 1 | 2 | 2 | 0.153694 | 0.014524 |

\* Corrected value obtained from confirmatory remeasurement; the original exported value was 0.022929.

Under the same reporting framework, the best physical distances were 0.017236 for deterministic grid search, 0.059319 for random search, 0.045000 for the strict-blind language-model-guided run, and 0.017375 for the resumed language-model-guided run. The resumed language-model condition contained 18 historical observations and six new physical experiments and should therefore be identified as a resumed run rather than interpreted as an independent 24-experiment comparison.

## 3. Supplementary Algorithm Listing

```text
Algorithm S1: CEID closed-loop spectral optimisation

Input:
    Experimentally captured target spectrum S*
    Feasible discrete formulation space X
    Initial design size N0 = 8
    Total physical experiment budget N = 24

Output:
    Best measured formulation x_best
    Best measured Frechet distance f_best
    Complete experimental trace D

1:  Generate N0 feasible space-filling formulations X0.
2:  Set D <- empty set.
3:  for each formulation x in X0 do
4:      Prepare x using the robotic liquid-handling platform.
5:      Measure and normalize the eight-channel AS7341 spectrum S(x).
6:      Compute y <- discrete_frechet(S(x), S*).
7:      Append (x, y) to D.
8:  end for
9:  while number_of_physical_experiments(D) < N do
10:     Fit or update the CEID probabilistic surrogate using D.
11:     Score feasible unevaluated formulations with the acquisition policy.
12:     Select the next batch subject to variable and volume constraints.
13:     for each selected formulation x in the batch do
14:         Prepare x using the robotic liquid-handling platform.
15:         Measure and normalize the eight-channel AS7341 spectrum S(x).
16:         Compute y <- discrete_frechet(S(x), S*).
17:         Append (x, y) to D.
18:     end for
19: end while
20: x_best <- formulation in D with the minimum measured y.
21: f_best <- minimum measured y in D.
22: return x_best, f_best, D.
```

The run used a fixed-budget stopping rule. If a numerical early-stop rule is introduced in a future protocol, its threshold must be specified before data collection and distinguished from the descriptive 0.03 and 0.02 thresholds reported above.

## 4. Gravimetric Dispensing Validation

Gravimetric dispensing validation was performed at nominal volumes of 200, 500, and 1,000 uL using five independent measurements per volume. The raw balance readings were:

| Target volume (uL) | Replicate 1 (mg) | Replicate 2 (mg) | Replicate 3 (mg) | Replicate 4 (mg) | Replicate 5 (mg) |
| ---: | ---: | ---: | ---: | ---: | ---: |
| 200 | 198.9 | 199.4 | 199.7 | 200.0 | 200.5 |
| 500 | 497.6 | 498.3 | 498.9 | 499.5 | 500.2 |
| 1,000 | 995.4 | 996.9 | 997.8 | 998.7 | 1,000.2 |

The mean, sample standard deviation, and coefficient of variation were calculated as

\[
\bar{m}=\frac{1}{n}\sum_{i=1}^{n}m_i,
\qquad
s=\sqrt{\frac{\sum_{i=1}^{n}(m_i-\bar{m})^2}{n-1}},
\qquad
CV=100\frac{s}{\bar{m}}.
\]

Mean volume was calculated from \(V=\bar{m}/\rho\), using a water density of 0.998 mg/uL.

| Target volume (uL) | n | Mean mass (mg) | SD (mg) | CV (%) | Calculated mean volume (uL) |
| ---: | ---: | ---: | ---: | ---: | ---: |
| 200 | 5 | 199.7 | 0.60 | 0.303 | 200.1 |
| 500 | 5 | 498.9 | 1.01 | 0.203 | 499.9 |
| 1,000 | 5 | 997.8 | 1.81 | 0.182 | 999.8 |

Manuscript text:

> Gravimetric validation was performed using five independent measurements at each nominal dispensing volume. Mean dispensed masses were 199.7 +/- 0.6 mg, 498.9 +/- 1.0 mg, and 997.8 +/- 1.8 mg for nominal volumes of 200, 500, and 1,000 uL, respectively. The corresponding coefficients of variation were 0.303%, 0.203%, and 0.182%. Using a water density of 0.998 mg/uL, the calculated mean volumes were 200.1, 499.9, and 999.8 uL, respectively. Values are reported as mean +/- sample standard deviation (n = 5), and error bars represent one standard deviation.

Figure caption:

> Figure X. Gravimetric validation of liquid dispensing at nominal volumes of 200, 500, and 1,000 uL. Points show the calculated mean dispensed volume from five independent measurements, error bars show one sample standard deviation, and the dashed line represents ideal agreement with the nominal volume.

Before submission, add the balance manufacturer and model, balance readability, calibration status, water temperature, and laboratory environmental conditions to the Methods section. The assumed density must be adjusted if the recorded water temperature requires a different value.

## 5. Patent Declaration

The authors declare that there are no patents or patent applications related to the work reported in this manuscript.

## 6. Data, Pseudocode, Licences, and Access

Manuscript-ready text:

> Pseudocode for CEID and the Unity digital-twin workflow, together with the sanitized closed-loop trace, target spectrum, gravimetric validation data, and manuscript-supporting files, is available at https://github.com/souhilsid/Self-Driving-Lab-Digital-Twin- under the MIT License. A versioned archival release of these materials will be deposited in Zenodo upon acceptance; the corresponding DOI will be added to the final manuscript. CEID implementation source, Unity implementation source, and the physical hardware implementation are not publicly distributed. Access for verification or non-commercial research may be requested from AISCIA Informatics through the corresponding author and is subject to approval and an appropriate access agreement. A browser-based WebGL build of the digital twin is available at https://souhilsid.github.io/Self-Driving-Lab-Digital-Twin-/.

Required final replacements before submission:

| Item | Required value |
| --- | --- |
| Zenodo archive | `[insert final Zenodo DOI]` |
| Reviewer WebGL build | `https://souhilsid.github.io/Self-Driving-Lab-Digital-Twin-/` (no installation or access code required) |
| CEID access request | `[insert corresponding-author or AISCIA contact email]` |
| Archived release version | `[insert version/tag and commit identifier]` |
| Hardware access request | `[insert request procedure or contact URL]` |

No CERN Open Hardware Licence should be claimed because the hardware is proprietary. The repository contains pseudocode and supporting evidence, not CEID or Unity implementation source. The licences included with the repository and future binary distribution must match the statements above.

## 7. Reviewer-Accessible Binary Statement

Manuscript-ready text:

> A reviewer-accessible binary of the digital twin is available as a browser-based WebGL build at https://souhilsid.github.io/Self-Driving-Lab-Digital-Twin-/. No installation or access code is required. Reviewers can open the URL in a WebGL-compatible desktop browser. The demonstration includes the digital-twin interface and representative closed-loop workflow but does not provide unrestricted access to the proprietary CEID service or physical robotic hardware. Requests for supervised access to those components may be directed to `[contact email]`.

## 8. Primary Supporting Records

- CEID pseudocode: [`../pseudocode/CEID.md`](../pseudocode/CEID.md)
- Unity digital-twin pseudocode: [`../pseudocode/UNITY_DIGITAL_TWIN.md`](../pseudocode/UNITY_DIGITAL_TWIN.md)
- Captured target spectrum: [`data/target_spectrum.json`](data/target_spectrum.json)
- Sanitized strategy summary: [`data/strategy_comparison_summary.csv`](data/strategy_comparison_summary.csv)
- Complete primary CEID trial trace: [`data/ceid_closed_loop_trace.csv`](data/ceid_closed_loop_trace.csv)
- Gravimetric measurements: [`data/gravimetric_repeatability_measured.csv`](data/gravimetric_repeatability_measured.csv)
- Gravimetric figure: [`data/gravimetric_repeatability_measured.png`](data/gravimetric_repeatability_measured.png)
