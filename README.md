# Android Client-Server Appium Demo

A small end-to-end learning project that combines native Android development, a local Python backend, structured logging, and Appium test automation.

The first use case is deliberately simple: the Android application connects to a local server, receives a JSON message, and displays the result. The same flow is tested on both an Android emulator and a physical Android device.

## Project Goals

- build and install a first Android APK;
- compare emulator and physical-device behavior;
- implement a small HTTP/JSON client-server flow;
- automate the user flow with Appium and pytest;
- establish useful logging and failure diagnostics;
- keep the project suitable for later CI or Raspberry Pi extensions.

## Planned Technology Stack

- **Android client:** Kotlin and Android Studio
- **Backend:** Python web service
- **UI automation:** Appium with the UiAutomator2 driver
- **Test framework:** pytest
- **Version control:** Git
- **Host environment:** Windows
- **Development targets:** Android Virtual Device and physical Android device

## Initial User Flow

```text
Start Android app
        |
        v
Connect to local backend
        |
        v
GET /hello
        |
        v
Receive JSON response
        |
        v
Display success or error state
        |
        v
Verify behavior with Appium
```

## Repository Structure

```text
android-app-demo/
├── README.md
├── .gitignore
├── docs/
│   ├── architecture.md
│   ├── setup.md
│   ├── test-strategy.md
│   └── lessons_learned.md
├── backend/
│   ├── app.py
│   ├── requirements.txt
│   ├── src/
│   └── tests/
├── mobile/
│   └── android-client/
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
└── logs/
    ├── backend/
    ├── android/
    ├── appium-server/
    └── test-automation/
```

Generated logs and test artifacts are excluded from Git. Empty log directories can be retained with `.gitkeep` files.

## Documentation

- [`docs/architecture.md`](docs/architecture.md): system structure, components, networking, logging, and testability decisions;
- [`docs/setup.md`](docs/setup.md): Windows, Android Studio, emulator, physical-device, backend, Appium, and Git setup;
- [`docs/test-strategy.md`](docs/test-strategy.md): scope, test levels, environments, automation design, risks, and exit criteria;
- [`docs/lessons_learned.md`](docs/lessons_learned.md): reusable findings, root causes, resolutions, and technical decisions.

## Prerequisites

The planned local setup requires:

- Git for Windows;
- Android Studio and Android SDK Platform Tools;
- Python 3;
- Node.js and npm;
- Appium;
- Appium UiAutomator2 driver;
- an Android Virtual Device or a physical Android device with USB debugging enabled.

See [`docs/setup.md`](docs/setup.md) for the detailed setup procedure.

## Quick Start

The implementation is not yet complete. The intended workflow is:

### 1. Clone or initialize the repository

```powershell
git clone <repository-url>
cd android-app-demo
```

For a new local-only repository, use `git init` instead.

### 2. Prepare the backend

```powershell
cd backend
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
python app.py
```

The first backend version will provide:

```text
GET /health
GET /hello
```

### 3. Start an Android target

For the emulator:

1. Open Android Studio.
2. Open **Tools > Device Manager**.
3. Start the configured Android Virtual Device.

For a physical device:

1. Enable Developer Options and USB debugging.
2. Connect the device through USB.
3. Accept the debugging authorization prompt.

Verify either target with:

```powershell
adb devices
```

### 4. Configure the backend address

The Android client uses different host addresses depending on the target:

- emulator: `http://10.0.2.2:<port>`;
- physical device: `http://<windows-lan-ip>:<port>`.

The exact value should be supplied through local environment configuration rather than scattered through the source code.

### 5. Build and run the Android application

Open `mobile/android-client` in Android Studio, select the target device, and run the application.

### 6. Start Appium

```powershell
appium --log logs\appium-server\appium-session.log
```

### 7. Run the automated tests

```powershell
cd test-automation\appium
.\.venv\Scripts\Activate.ps1
pytest
```

The concrete commands and configuration files will be finalized as implementation progresses.

## Logging

The project separates logs by source:

```text
logs/
├── backend/
├── android/
├── appium-server/
└── test-automation/
```

A useful log entry should identify:

- timestamp;
- severity;
- component;
- session or correlation ID;
- operation and result.

Never log passwords, tokens, complete authorization headers, personal data, or sensitive device identifiers.

For Android debugging, use Android Studio Logcat or capture output through ADB:

```powershell
adb logcat -c
adb logcat > logs\android\logcat-session.txt
```

## Testing Approach

The project uses several complementary test levels:

- backend unit tests;
- backend API tests;
- focused Android unit or component tests;
- Appium end-to-end tests on an emulator;
- milestone Appium tests on a physical device.

The first Appium test verifies that the application can connect to the backend and display the expected response. A second scenario verifies the visible error state when the backend is unavailable.

See [`docs/test-strategy.md`](docs/test-strategy.md) for details.

## Testability Guidelines

- give interactive UI elements stable accessibility or resource identifiers;
- prefer explicit waits over fixed sleeps;
- keep emulator and physical-device configuration separate;
- keep tests independent and deterministic;
- capture screenshot, page source, Logcat, Appium, pytest, and backend evidence on failure;
- use Page Objects to separate UI interaction from test intent.

## Current Status

- [x] Initial repository structure defined
- [x] Architecture documentation created
- [x] Setup documentation created
- [x] Test strategy created
- [x] Lessons-learned template created
- [ ] Android project created
- [ ] Backend implemented
- [ ] Structured application logging implemented
- [ ] Appium environment validated
- [ ] Emulator end-to-end test implemented
- [ ] Physical-device end-to-end test implemented

## Security and Repository Hygiene

Do not commit:

- secrets or `.env` files;
- signing keystores;
- `local.properties`;
- personal device identifiers;
- generated APK/AAB files;
- runtime logs, screenshots, videos, or test reports;
- Python virtual environments or Node modules.

Provide sanitized `.example` configuration files when local values are required.

## Planned Next Steps

1. Create the Kotlin Android project under `mobile/android-client`.
2. Implement the minimal backend with `/health` and `/hello`.
3. Add Android network access and explicit UI states.
4. Add structured backend and Android logging.
5. Create emulator and physical-device Appium profiles.
6. Implement the first successful and unsuccessful connection tests.
7. Add automatic artifacts for failed test executions.

## License

No license has been selected yet. Add a `LICENSE` file before publishing or distributing the repository beyond its intended private use.
