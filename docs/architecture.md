# Architecture

## 1. Purpose

This repository contains a small end-to-end learning project consisting of:

- a native Android application,
- a local HTTP backend,
- Appium-based UI test automation,
- shared test data and scripts,
- diagnostic logs for debugging on both an emulator and a physical Android device.

The first use case is intentionally small: when the Android application is opened, it requests a message from the backend and displays the result. The same behavior is then verified automatically with Appium.

## 2. System Context

```text
+----------------------+       HTTP/JSON       +----------------------+
| Android application  | --------------------> | Local backend        |
| Emulator or device   | <-------------------- | Python web service   |
+----------+-----------+                       +----------+-----------+
           ^                                              |
           | WebDriver/Appium                             | Backend logs
           |                                              v
+----------+-----------+                       +----------------------+
| Appium test suite    | --------------------> | logs/backend/        |
| Python + pytest      |                       +----------------------+
+----------+-----------+
           |
           | Test results, screenshots, driver logs,
           | and optional Logcat captures
           v
+----------------------+
| logs/test-automation/|
+----------------------+
```

## 3. Repository Structure

```text
android-app-demo/
├── README.md
├── .gitignore
├── docs/
│   ├── architecture.md
│   ├── setup.md
│   └── lessons_learned.md
├── backend/
│   ├── app.py
│   ├── requirements.txt
│   ├── src/
│   └── tests/
├── mobile/
│   └── android-client/
│       ├── app/
│       ├── gradle/
│       ├── build.gradle.kts
│       └── settings.gradle.kts
├── test-automation/
│   └── appium/
│       ├── tests/
│       ├── pages/
│       ├── utils/
│       ├── config/
│       ├── conftest.py
│       └── requirements.txt
├── test-data/
│   ├── requests/
│   └── responses/
├── scripts/
│   ├── start_backend.ps1
│   ├── start_emulator.ps1
│   ├── capture_logcat.ps1
│   └── run_tests.ps1
└── logs/
    ├── backend/
    ├── android/
    ├── appium-server/
    └── test-automation/
```

The `logs/` subdirectories are kept in Git with placeholder files, while generated log content is ignored.

## 4. Components

### 4.1 Android Client

The Android client is responsible for:

- presenting the connection status,
- calling the backend over HTTP,
- parsing the JSON response,
- displaying success and error states,
- producing useful application logs without exposing secrets.

Stable accessibility identifiers should be assigned to test-relevant UI elements. Appium tests should primarily use these identifiers instead of coordinates, visible text, or fragile XPath expressions.

Suggested first-screen elements:

- connection button,
- status label,
- response label,
- optional retry button.

### 4.2 Backend

The backend provides a minimal API such as:

```text
GET /health
GET /hello
```

Example response:

```json
{
  "message": "Hello from the server"
}
```

The backend should log:

- service startup and shutdown,
- request method and path,
- response status,
- request duration,
- exceptions with stack traces.

### 4.3 Appium Test Automation

The Appium test layer controls the Android application on either:

- an Android Virtual Device (AVD), or
- a physical Android device connected through ADB.

The first end-to-end test should:

1. confirm that the backend is reachable,
2. start or reset the application,
3. trigger the connection,
4. wait for the response state,
5. verify the displayed server message,
6. save diagnostic artifacts if the test fails.

Device-specific settings should be configuration data rather than hard-coded in the test.

### 4.4 Scripts

PowerShell scripts provide repeatable local commands for starting services, capturing logs, and running tests. Scripts should return non-zero exit codes on failure so that they can later be reused in CI.

## 5. Network Configuration

The backend address differs by execution target:

- Android Emulator: use the Android emulator host alias, commonly `10.0.2.2`.
- Physical device: use the Windows host's LAN IP address, provided the device and computer can communicate on the same network.

The base URL should be injected through build configuration, environment configuration, or a local developer setting. It should not be duplicated across production code and tests.

## 6. Logging Strategy

### 6.1 Goals

Logging should answer four questions:

1. What operation was attempted?
2. On which device and environment did it run?
3. What was the result?
4. Where did a failure occur?

### 6.2 Log Sources

| Source | Intended content | Target location |
|---|---|---|
| Android application | lifecycle, request flow, response status, handled errors | Logcat and optional captured file under `logs/android/` |
| Backend | startup, requests, status codes, durations, exceptions | `logs/backend/` |
| Appium server | session creation, commands, driver and device errors | `logs/appium-server/` |
| pytest/Appium tests | test steps, assertions, retries, artifact paths | `logs/test-automation/` |

### 6.3 Log Levels

- `DEBUG`: detailed development and diagnostic information.
- `INFO`: normal startup, connection, request, and test milestones.
- `WARNING`: recoverable or unexpected conditions.
- `ERROR`: failed operations and exceptions.

### 6.4 Format

Prefer a consistent format containing:

```text
UTC timestamp | level | component | correlation ID | message
```

A correlation ID should be generated for a test or request flow and propagated where practical. This makes it easier to connect Android, backend, and Appium events.

### 6.5 Security and Repository Hygiene

Never log:

- passwords,
- API keys or tokens,
- complete authorization headers,
- sensitive personal data.

Generated logs, screenshots, videos, APKs, local SDK paths, virtual environments, and secrets must not be committed. A small sanitized example log may be stored separately under documentation if it is useful.

## 7. Testability Guidelines

- Give interactive UI elements stable accessibility identifiers.
- Keep network calls behind a small client/service abstraction.
- Represent loading, success, and error states explicitly.
- Use explicit waits in Appium rather than fixed sleep statements.
- Capture a screenshot, page source, Appium output, and Logcat excerpt on failure.
- Keep emulator and physical-device capabilities in separate configuration files.
- Make backend responses deterministic for the initial end-to-end test.

## 8. Initial Scope and Future Growth

### Initial scope

- one Android screen,
- one backend endpoint,
- one emulator configuration,
- one physical-device configuration,
- one automated happy-path test,
- basic failure diagnostics.

### Possible later extensions

- negative network and timeout tests,
- authentication,
- persistent storage,
- Dockerized backend,
- CI execution with an emulator,
- Raspberry Pi or smart-home integration,
- performance and reliability measurements.
