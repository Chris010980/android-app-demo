# Test Strategy

## 1. Purpose

This document defines the test approach for the Android client, local Python backend, and Appium-based end-to-end automation. The project is intentionally small, but the strategy establishes practices that can scale to a larger mobile or IoT solution.

The initial business flow is:

1. The Android application starts.
2. The application connects to the configured backend.
3. The backend returns a deterministic JSON response.
4. The application displays a clear success or error state.
5. Appium verifies the flow on an emulator and a physical Android device.

## 2. Quality Objectives

The project should demonstrate that:

- the Android application can be built and installed reliably;
- the application behaves consistently on an emulator and a physical device;
- the client handles successful and unsuccessful backend communication visibly;
- the backend contract is deterministic and independently testable;
- the main user flow can be executed repeatedly through Appium;
- test failures produce enough evidence for efficient root-cause analysis;
- environment-specific values are configurable and not hard-coded in test logic.

## 3. Scope

### 3.1 In Scope for the Initial Version

- Android application launch and initial UI state;
- connection to the local backend;
- `GET /health` and `GET /hello` backend endpoints;
- successful response display;
- backend unavailable or connection failure state;
- emulator execution;
- physical-device execution;
- backend unit/API tests;
- Android unit tests for isolated logic where useful;
- Appium end-to-end tests;
- logging and diagnostic artifact collection.

### 3.2 Out of Scope Initially

- iOS support;
- Google Play publication;
- authentication and authorization;
- production hosting;
- load and endurance testing;
- accessibility certification;
- formal security penetration testing;
- broad Android device compatibility testing;
- automated CI execution.

These items may be added when the basic local workflow is stable.

## 4. Test Levels

### 4.1 Backend Unit Tests

Backend unit tests verify application logic without requiring an Android device.

Initial checks should cover:

- expected response body;
- expected HTTP status code;
- invalid route behavior;
- exception handling;
- logging behavior where practical.

### 4.2 Backend API Tests

API tests call the running service directly and verify:

- `/health` confirms service availability;
- `/hello` returns valid JSON;
- required response fields exist;
- content type and status code are correct;
- response time is recorded for observation, without defining a strict performance target initially.

### 4.3 Android Unit Tests

Use local Android unit tests for isolated logic such as:

- response mapping;
- connection-state mapping;
- input validation;
- formatting of displayed messages.

Network and UI framework behavior should not be forced into local unit tests when a component or instrumentation test is more appropriate.

### 4.4 Android Instrumented or UI Component Tests

Add focused Android tests only where they provide faster feedback than full Appium tests, for example:

- screen state after a successful mocked response;
- loading and error-state rendering;
- lifecycle-sensitive behavior.

### 4.5 Appium End-to-End Tests

Appium verifies the complete externally visible flow through the installed APK and a running backend.

The first automated scenarios are:

#### E2E-001: Successful server connection

1. Confirm that the backend is available.
2. Launch or reset the Android application.
3. Verify the initial state.
4. Trigger the server connection if it is not automatic.
5. Wait for the successful result.
6. Verify the displayed server message.

#### E2E-002: Backend unavailable

1. Ensure that the backend is not reachable or configure an invalid endpoint.
2. Launch the application.
3. Trigger the connection.
4. Verify that a clear error state is displayed.
5. Verify that the application remains responsive.

#### E2E-003: Retry after failure

1. Produce a connection failure.
2. Restore backend availability.
3. Trigger retry.
4. Verify successful recovery without reinstalling the application.

## 5. Test Environments

### 5.1 Android Emulator

The emulator is the primary development and repeatability environment. It is suitable for frequent local executions, clean-state testing, and later CI integration.

Record for each execution:

- AVD name;
- Android/API version;
- screen profile;
- application build variant;
- Appium and UiAutomator2 driver versions.

The emulator reaches the Windows host backend through its configured host alias, normally `10.0.2.2`.

### 5.2 Physical Android Device

The physical device validates behavior under real hardware, USB/ADB communication, device-specific permissions, and actual WLAN connectivity.

Record only non-sensitive environment details, such as:

- device model;
- Android version;
- application build variant;
- connection type;
- Appium and UiAutomator2 driver versions.

Do not commit personal device identifiers.

### 5.3 Backend Environment

The initial backend runs locally on Windows. Configuration should define:

- host and port;
- log level;
- optional deterministic response values;
- development mode.

The service should expose a health endpoint so that automated tests can fail early if the test environment is not ready.

## 6. Test Data and Configuration

Test data belongs under `test-data/` when it is shared or represents API input/output examples. Small scenario-specific values may remain close to their tests.

Configuration principles:

- separate emulator and physical-device profiles;
- no secrets in source control;
- no hard-coded device IDs in test code;
- no duplicated backend URLs across page objects and tests;
- provide sanitized example configuration files;
- validate required configuration before starting a test session.

Suggested local files:

```text
test-automation/appium/config/
├── emulator.example.yaml
├── physical-device.example.yaml
├── emulator.local.yaml
└── physical-device.local.yaml
```

The `*.local.yaml` files should be ignored by Git.

## 7. Automation Design

### 7.1 Framework

The initial UI automation stack is:

- Appium server;
- UiAutomator2 Android driver;
- Appium Python client;
- pytest;
- Page Object Model for screen behavior;
- explicit waits for state transitions.

### 7.2 Locator Strategy

Use locators in this priority order:

1. accessibility identifier or stable semantic identifier;
2. Android resource ID;
3. a narrowly scoped platform selector;
4. XPath only as a last resort.

Do not use screen coordinates for functional tests. Visible text may be used for assertions but should not be the default interaction locator if the text can change or be localized.

### 7.3 Page Objects

Page objects should:

- expose user-relevant actions and states;
- hide locator details;
- avoid test assertions where possible;
- avoid direct backend setup;
- remain small enough to reflect one screen or coherent component.

Example responsibilities for `MainPage`:

```text
wait_until_loaded()
connect_to_server()
get_status_text()
wait_for_success()
wait_for_error()
```

### 7.4 Synchronization

Use explicit waits for observable conditions, such as:

- element visible;
- button enabled;
- loading indicator absent;
- expected status displayed.

Avoid fixed sleep statements except for short, documented diagnostic experiments. A passing test should not depend on arbitrary timing.

### 7.5 Test Independence

Each automated test should:

- establish its required backend state;
- start from a defined app state;
- avoid depending on a previous test;
- clean up temporary data where relevant;
- be executable individually.

## 8. Logging and Diagnostic Evidence

### 8.1 Normal Test Execution

Record:

- test session ID or correlation ID;
- start and end time;
- selected environment profile;
- application/build information;
- test name and result;
- relevant backend request IDs.

### 8.2 Failure Artifacts

On an Appium test failure, collect where possible:

- device screenshot;
- current Android page source;
- Appium server log;
- pytest log and stack trace;
- filtered Logcat output;
- backend log excerpt;
- environment metadata.

Suggested structure:

```text
logs/test-automation/<session-id>/<test-name>/
├── failure.png
├── page-source.xml
├── test.log
├── logcat.txt
├── appium.log
├── backend.log
└── environment.json
```

Generated diagnostic data must remain excluded from Git.

## 9. Entry and Exit Criteria

### 9.1 Entry Criteria for Appium Testing

- the APK builds successfully;
- the target device is visible through ADB;
- the Appium server and UiAutomator2 driver are available;
- the backend health check passes for positive scenarios;
- environment configuration is valid;
- required UI identifiers are implemented.

### 9.2 Exit Criteria for the Initial Milestone

- the backend unit/API tests pass;
- the Android application runs on the selected emulator;
- the Android application runs on the physical device;
- the successful connection test passes on both targets;
- the backend-unavailable behavior has been tested;
- failure artifacts have been demonstrated at least once;
- known limitations are recorded in `lessons_learned.md` or as repository issues.

## 10. Defect and Failure Classification

Classify failures before changing the test:

- application defect;
- backend defect;
- test automation defect;
- environment or configuration issue;
- device/emulator issue;
- network issue;
- dependency or tooling issue;
- expected behavior not yet specified.

A failed automated test is evidence of a problem, not automatically evidence of an application defect.

## 11. Risks and Mitigations

| Risk | Mitigation |
|---|---|
| Different backend addresses for emulator and device | Use environment-specific configuration and a startup health check. |
| Flaky UI timing | Use explicit waits for observable states and deterministic backend responses. |
| Fragile element locators | Design stable accessibility/resource identifiers into the application. |
| Missing diagnostic evidence | Capture screenshot, page source, Logcat, Appium, pytest, and backend logs on failure. |
| Local firewall blocks the physical device | Document the port and validate connectivity before starting Appium. |
| Test modifies shared state | Keep the first backend stateless and make every test establish its own prerequisites. |
| Sensitive data in logs | Use synthetic data and redact tokens, personal data, and device identifiers. |
| Emulator-only confidence | Execute milestone tests on both emulator and physical hardware. |

## 12. Reporting

The first version can use pytest console output and JUnit XML. Reports should show:

- test name;
- target environment;
- result;
- duration;
- failure message;
- paths to diagnostic artifacts.

Trend dashboards and advanced reporting are intentionally deferred until repeated execution produces meaningful history.

## 13. Future Test Extensions

Potential later additions include:

- malformed and delayed backend responses;
- retries and timeout boundaries;
- network interruption during a request;
- orientation and lifecycle tests;
- multiple API levels and screen profiles;
- installation, update, and uninstall tests;
- accessibility checks;
- backend contract tests;
- basic performance measurements;
- CI execution with an Android emulator;
- Raspberry Pi or smart-home integration scenarios.
