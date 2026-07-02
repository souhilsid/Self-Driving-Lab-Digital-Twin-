# Unity Digital-Twin Workflow Pseudocode

## Application and Navigation

```text
PROCEDURE START_DIGITAL_TWIN():
    current_page <- HOME
    connection_state <- DISCONNECTED
    control_mode <- MANUAL
    experiment_state <- IDLE
    SHOW_HOME_PAGE()

ON USER_NAVIGATES(page):
    VALIDATE_PAGE(page)
    HIDE_ALL_PAGES()
    SHOW(page)
    current_page <- page
```

## Objective Entry and Connection

```text
ON USER_SUBMITS_OBJECTIVE(red, green, blue):
    REQUIRE 0 <= red, green, blue <= 255
    target <- {R: red, G: green, B: blue}
    DISPLAY_TARGET(target)
    STORE_PENDING_TARGET(target)

ON USER_CLICKS_CONNECT():
    REQUIRE pending_target EXISTS
    short_lived_token <- REQUEST_TOKEN_FROM_TRUSTED_SERVICE()
    CONNECT_LIVEKIT(short_lived_token, room, topic)
    WAIT_FOR_PARTICIPANT(identity = "sdl")
    SEND_TARGET_TO_CEID(pending_target)
    connection_state <- CONNECTED
    DISPLAY_CONNECTION_STATUS(CONNECTED)
```

## Autonomous CEID-Controlled Execution

```text
ON USER_SELECTS_AUTONOMOUS_MODE():
    REQUIRE experiment_state IN {IDLE, PAUSED, STOPPED}
    REQUIRE hardware_state IS SAFE
    control_mode <- SDL
    SEND_CONTROL_POLICY(mode = SDL, digital_twin_control = TRUE)

ON CEID_CANDIDATE_RECEIVED(candidate):
    VALIDATE_RECIPE_BOUNDS_AND_TOTAL_VOLUME(candidate)
    DISPLAY_CANDIDATE(candidate)
    experiment_state <- READY

    IF require_user_confirmation:
        WAIT_FOR_USER_START_OR_CONTINUE()

    SEND_EXECUTE(candidate)
    experiment_state <- RUNNING

ON SDL_STATUS_RECEIVED(status):
    UPDATE_MOTION_AND_PROCESS_VISUALS(status)
    UPDATE_PROGRESS_CONTROLS(status)

ON SDL_MEASUREMENT_RECEIVED(measurement):
    DISPLAY_MEASURED_RGB_AND_SPECTRUM(measurement)
    FORWARD_RESULT_TO_CEID(measurement)
    APPEND_EXPERIMENT_HISTORY(measurement)
    experiment_state <- READY_FOR_NEXT_CANDIDATE
```

## Manual and Immersive Control

```text
ON USER_SELECTS_MANUAL_OR_IMMERSIVE_MODE():
    IF experiment_state = RUNNING:
        REQUEST_CONTROLLED_STOP()
        WAIT_UNTIL_HARDWARE_IS_IDLE()

    control_mode <- MANUAL
    SEND_CONTROL_POLICY(mode = MANUAL, digital_twin_control = TRUE)
    ENABLE_MANUAL_CONTROLS()

ON USER_SENDS_MANUAL_ACTION(action):
    REQUIRE control_mode = MANUAL
    REQUIRE connection_state = CONNECTED
    REQUIRE experiment_state != EMERGENCY_STOPPED
    VALIDATE_ACTION_LIMITS(action)
    SEND_ACTION_TO_SDL(action)
    WAIT_FOR_ACKNOWLEDGEMENT_OR_TIMEOUT()
    UPDATE_DIGITAL_TWIN_FROM_CONFIRMED_STATUS()
```

## Emergency Stop and Recovery

```text
ON USER_CLICKS_EMERGENCY_STOP():
    SEND_RELIABLE_EMERGENCY_STOP_TO_SDL()
    DISABLE_START_CONTINUE_AND_MANUAL_MOTION()
    experiment_state <- EMERGENCY_STOPPED
    DISPLAY_EMERGENCY_STOPPED()

ON SDL_HANDLES_EMERGENCY_STOP():
    ABORT_ACTIVE_ROBOT_COMMAND()
    TURN_ALL_RELAYS_OFF()
    STOP_MOTION_CONTROLLER()
    LATCH_EMERGENCY_STATE()
    SEND_STOP_CONFIRMATION_TO_UNITY()

ON USER_REQUESTS_RECOVERY():
    REQUIRE PHYSICAL_AREA_CONFIRMED_SAFE
    REQUIRE SDL_EMERGENCY_LATCH_RESET
    RECONNECT_IF_REQUIRED()
    HOME_OR_REINITIALISE_HARDWARE()
    DISCARD_INTERRUPTED_TRIAL()
    experiment_state <- IDLE
```

An interrupted physical trial is not resumed from the middle. Recovery resets the hardware to a known state, after which the trial can be restarted as a new execution.
