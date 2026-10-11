# Setup Guide

## 1. Purpose

This guide describes the planned local development environment for the Android client, Python backend, and Appium tests on Windows. Android Studio runs directly on Windows. WSL is not required for the initial project.

## 2. Prerequisites

Install the following tools:

- Git for Windows,
- Android Studio,
- Android SDK Platform Tools,
- a supported Python 3 version,
- Node.js and npm,
- Appium,
- the Appium UiAutomator2 driver,
- optional: Appium Inspector.

Useful checks in PowerShell:

```powershell
git --version
python --version
node --version
npm --version
adb version
```

## 3. Create the Repository

```powershell
mkdir android-app-demo
cd android-app-demo
git init
git branch -M main
```

Create the initial directories:

```powershell
$directories = @(
    "docs",
    "backend/src",
    "backend/tests",
    "mobile",
    "test-automation/appium/tests",
    "test-automation/appium/pages",
    "test-automation/appium/utils",
    "test-automation/appium/config",
    "test-data/requests",
    "test-data/responses",
    "scripts",
    "logs/backend",
    "logs/android",
    "logs/appium-server",
    "logs/test-automation"
)

$directories | ForEach-Object { New-Item -ItemType Directory -Force -Path $_ }
```

Keep the empty log directories:

```powershell
New-Item logs/backend/.gitkeep -ItemType File
New-Item logs/android/.gitkeep -ItemType File
New-Item logs/appium-server/.gitkeep -ItemType File
New-Item logs/test-automation/.gitkeep -ItemType File
```

## 4. Recommended `.gitignore`

```gitignore
# Android and Gradle
.gradle/
**/build/
local.properties
captures/
.externalNativeBuild/
.cxx/
*.apk
*.aab

# Android Studio / IntelliJ
.idea/
*.iml

# Python
__pycache__/
*.py[cod]
.venv/
.pytest_cache/
.coverage
htmlcov/

# Node and Appium
node_modules/
.npm/

# Runtime logs and test artifacts
logs/**/*.log
logs/**/*.txt
logs/**/*.json
logs/**/*.xml
logs/**/*.html
logs/**/*.png
logs/**/*.jpg
logs/**/*.mp4
!logs/**/.gitkeep
reports/
screenshots/

# Environment and secrets
.env
.env.*
!.env.example
*.keystore
*.jks

# Operating systems
Thumbs.db
.DS_Store
```

Review `.idea/` handling once Android Studio has created the project. A small team may intentionally version selected project settings, but the simplest starting point is to ignore the directory.

## 5. Android Studio and Git

Android Studio already includes Git integration. No additional plugin is required for normal local repository work such as viewing changes, committing, branching, merging, and inspecting history.

After Git for Windows is installed:

1. Open Android Studio.
2. Open **Settings > Version Control > Git**.
3. Verify the path to `git.exe` with **Test**.
4. Open the repository root rather than only the nested Android module.
5. If necessary, select **VCS > Enable Version Control Integration > Git**.

Optional plugins should only be added for a clear need. Reasonable later candidates include a `.gitignore` helper or static-analysis integration, but they are not prerequisites. Avoid adding many plugins during initial setup because they add configuration and upgrade dependencies without improving the first learning objective.

## 6. Create the Android Project

In Android Studio:

1. Select **New Project**.
2. Choose a minimal application template.
3. Use Kotlin.
4. Set the project location to:

```text
<repository-root>\mobile\android-client
```

5. Let Gradle synchronization complete.
6. Run the unmodified application before adding backend communication.

Do not create another Git repository inside `mobile/android-client`. The repository root already owns all components.

## 7. Configure an Emulator

1. Open **Tools > Device Manager**.
2. Create an Android Virtual Device.
3. Select a phone profile and system image.
4. Start the virtual device.
5. Confirm that ADB can see it:

```powershell
adb devices
```

For a backend running on the same Windows computer, plan to use:

```text
http://10.0.2.2:<port>
```

The final port will be documented when the backend implementation is added.

## 8. Configure a Physical Android Device

1. Enable Developer Options on the device.
2. Enable USB debugging.
3. Connect the device through USB.
4. Accept the debugging authorization prompt.
5. Verify the connection:

```powershell
adb devices
```

For direct WLAN communication, use the Windows computer's LAN IP address instead of `localhost`. Windows Firewall and the active network profile must permit the selected backend port.

## 9. Backend Environment

Create a virtual environment:

```powershell
cd backend
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
```

Dependencies will be recorded in `backend/requirements.txt`. The first implementation should provide `/health` and `/hello` endpoints and write rotating or session-based logs under `logs/backend/`.

## 10. Appium Environment

Install Appium and the Android driver:

```powershell
npm install --global appium
appium driver install uiautomator2
appium driver list --installed
```

Create the Python test environment:

```powershell
cd test-automation\appium
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
```

The Python requirements will later include the Appium Python client, pytest, and any selected reporting packages.

Keep emulator and physical-device options in separate local configuration files. Do not commit device identifiers if that would disclose personal or organization-specific information.

## 11. Logging and Debugging

### Android application logs

Use Android Studio's **Logcat** window during development. Filter by the application package or a consistent application tag.

A basic command-line capture can later be wrapped by `scripts/capture_logcat.ps1`:

```powershell
adb logcat -c
adb logcat > logs\android\logcat-session.txt
```

Stop the capture with `Ctrl+C`.

### Backend logs

The backend should write both concise console output and a file log. File logging must include timestamps, severity, component name, and exception details.

### Appium server logs

Start Appium with file output when diagnostic persistence is needed:

```powershell
appium --log logs\appium-server\appium-session.log
```

### Automated test logs

pytest should write a session log to `logs/test-automation/`. On failure, the framework should save:

- a screenshot,
- the current page source,
- the Appium session information,
- a relevant Logcat excerpt,
- the assertion error and stack trace.

## 12. First Commit

Before committing, inspect the repository:

```powershell
git status
git diff
```

Then create the first commit:

```powershell
git add .
git commit -m "Initialize Android client, backend, and Appium project structure"
```

## 13. Planned Development Order

1. Verify the Android template on the emulator.
2. Verify it on the physical device.
3. Implement the backend health and hello endpoints.
4. Add Android networking and clear UI states.
5. Add structured logging to app and backend.
6. Install and validate Appium against the emulator.
7. Run the same test against the physical device.
8. Add automatic diagnostic artifacts for failed tests.
