# CEID Closed-Loop Optimisation Pseudocode

## Inputs

- Experimentally captured normalized target spectrum `target_spectrum`.
- Feasible discrete recipe space `X` for red, yellow, blue, and water units.
- Per-variable bounds `1..5` and composition constraint `sum(recipe) <= 13`.
- Initial design size `N0 = 8`.
- Total physical experiment budget `N = 24`.

## Algorithm

```text
PROCEDURE CEID_OPTIMISE(target_spectrum, X, N0, N):
    observations <- empty dataset
    initial_recipes <- SPACE_FILLING_DESIGN(X, N0)

    FOR EACH recipe IN initial_recipes:
        result <- EXECUTE_PHYSICAL_EXPERIMENT(recipe)
        measured_spectrum <- NORMALISE_AS7341(result.sensor_channels)
        error <- DISCRETE_FRECHET(measured_spectrum, target_spectrum)
        observations.ADD(recipe, error, result.measurement_uncertainty)

    WHILE observations.physical_count < N:
        surrogate <- FIT_PROBABILISTIC_MODEL(observations)
        feasible <- UNEVALUATED_RECIPES(X, observations)
        scores <- ACQUISITION_SCORE(surrogate, feasible)
        batch <- SELECT_CONSTRAINED_BATCH(feasible, scores, batch_size = 4)

        FOR EACH recipe IN batch:
            result <- EXECUTE_PHYSICAL_EXPERIMENT(recipe)
            measured_spectrum <- NORMALISE_AS7341(result.sensor_channels)
            error <- DISCRETE_FRECHET(measured_spectrum, target_spectrum)
            observations.ADD(recipe, error, result.measurement_uncertainty)

    best <- ARG_MIN(observations, key = error)
    RETURN best.recipe, best.error, observations
```

## Objective

```text
FUNCTION DISCRETE_FRECHET(measured, target):
    FOR i FROM 1 TO LENGTH(measured):
        FOR j FROM 1 TO LENGTH(target):
            point_error <- EUCLIDEAN_DISTANCE(measured[i], target[j])

            IF i = 1 AND j = 1:
                cost[i,j] <- point_error
            ELSE IF i = 1:
                cost[i,j] <- MAX(cost[i,j-1], point_error)
            ELSE IF j = 1:
                cost[i,j] <- MAX(cost[i-1,j], point_error)
            ELSE:
                predecessor <- MIN(cost[i-1,j], cost[i-1,j-1], cost[i,j-1])
                cost[i,j] <- MAX(predecessor, point_error)

    RETURN cost[LENGTH(measured), LENGTH(target)]
```

The production run used a fixed 24-experiment stopping rule. It did not automatically stop at a numerical tolerance. The final incumbent was first identified at trial 16 and remained unchanged through trial 24.
