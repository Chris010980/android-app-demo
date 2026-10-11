# Roadmap v0.1

## Goal

Create a minimal but complete end-to-end Android application that:

- runs on an Android emulator,
- runs on a physical Android device,
- communicates with a local Python backend,
- displays a backend response,
- provides useful debug logging,
- is automatically tested using Appium.

---

# Epic 1 - Repository Bootstrap

## [x] ISSUE-001 Create repository structure

### Description

Create the initial repository structure, documentation skeleton and Git repository.

### Tasks

- [x] Create root repository
- [x] Create directory structure
- [x] Add `.gitignore`
- [x] Add placeholder `.gitkeep` files
- [x] Add documentation files

### Acceptance Criteria

- [x] Repository structure matches `architecture.md`
- [x] Repository can be committed
- [x] No generated files are committed

### Suggested Commit

```text
Initialize repository structure
```

---

## [x] ISSUE-002 Configure development tools

### Description

Install and validate all required development tooling.

### Tasks

- [x] Install Android Studio
- [x] Install Android SDK
- [x] Install Git
- [x] Install Python
- [x] Install Node.js
- [x] Install Appium
- [x] Install UiAutomator2 Driver

### Acceptance Criteria

- [x] The following commands execute successfully:

```powershell
git --version
python --version
npm --version
appium --version
adb version
```

### Suggested Commit

```text
Validate local development environment
```

---

# Epic 2 - Android Client Foundation

## [ ] ISSUE-003 Create Android application

### Description

Create the first Android application skeleton.

### Tasks

- [ ] Create Android Studio project
- [ ] Verify Gradle sync
- [ ] Build APK
- [ ] Run application

### Acceptance Criteria

- [ ] APK builds successfully
- [ ] Application starts on emulator

### Suggested Commit

```text
Create Android project skeleton
```

---

## [ ] ISSUE-004 Add application logging foundation

### Description

Create a standardized Android logging strategy.

### Tasks

- [ ] Create logging helper
- [ ] Define log tags
- [ ] Log application startup
- [ ] Log lifecycle events

### Acceptance Criteria

- [ ] Logcat contains application startup information
- [ ] Logcat contains the creation of the main activity
- [ ] Logs can be filtered using a consistent application tag

Example:

```text
INFO Application started
INFO MainActivity created
```

### Suggested Commit

```text
Add Android logging foundation
```

---

## [ ] ISSUE-005 Create main screen

### Description

Create the initial user interface.

### Tasks

- [ ] Add title
- [ ] Add connect button
- [ ] Add status label
- [ ] Add response label

### Acceptance Criteria

- [ ] The screen displays the application title
- [ ] The Connect button is visible and enabled
- [ ] The initial status is `Not Connected`
- [ ] The layout renders correctly on the selected emulator

Example:

```text
Server Demo

[ Connect ]

Status:
Not Connected
```

### Suggested Commit

```text
Create initial application screen
```

---

## [ ] ISSUE-006 Add Appium-friendly identifiers

### Description

Prepare the user interface for stable test automation.

### Tasks

- [ ] Add resource IDs
- [ ] Add accessibility identifiers where appropriate
- [ ] Verify identifiers with Android Studio tooling or Appium Inspector

### Acceptance Criteria

- [ ] All interactive elements are uniquely identifiable
- [ ] Status and response fields can be read through Appium
- [ ] Functional tests do not require screen coordinates

### Suggested Commit

```text
Add automation identifiers
```

---

# Epic 3 - Backend Foundation

## [ ] ISSUE-007 Create Flask backend skeleton

### Description

Create the local backend service.

### Tasks

- [ ] Create Flask application
- [ ] Add `requirements.txt`
- [ ] Add startup script
- [ ] Add configuration handling

### Acceptance Criteria

- [ ] Backend starts successfully
- [ ] Backend host and port are configurable
- [ ] Dependencies can be installed from `requirements.txt`

### Suggested Commit

```text
Create backend skeleton
```

---

## [ ] ISSUE-008 Implement health endpoint

### Description

Create an endpoint for environment readiness checks.

### Tasks

- [ ] Create `/health`
- [ ] Return HTTP 200
- [ ] Return JSON content
- [ ] Add request logging

### Acceptance Criteria

- [ ] `GET /health` returns HTTP 200
- [ ] The response is valid JSON
- [ ] The response contains `status: healthy`

Example:

```json
{
  "status": "healthy"
}
```

### Suggested Commit

```text
Add health endpoint
```

---

## [ ] ISSUE-009 Implement hello endpoint

### Description

Create the first functional backend endpoint.

### Tasks

- [ ] Create `/hello` endpoint
- [ ] Return JSON response
- [ ] Log request and response status

### Acceptance Criteria

- [ ] `GET /hello` returns HTTP 200
- [ ] The response is valid JSON
- [ ] The response contains the expected message

Example:

```json
{
  "message": "Hello from server"
}
```

### Suggested Commit

```text
Add hello endpoint
```

---

## [ ] ISSUE-010 Add backend logging

### Description

Implement structured backend logging for debugging and test analysis.

### Tasks

- [ ] Create backend log configuration
- [ ] Write logs to `logs/backend/`
- [ ] Log service startup and shutdown
- [ ] Log requests and status codes
- [ ] Log exceptions with stack traces
- [ ] Add log rotation or session-based log files

### Acceptance Criteria

- [ ] Backend creates a log file under `logs/backend/`
- [ ] Log entries contain timestamp, level, component and message
- [ ] Requests to `/health` and `/hello` are visible in the log
- [ ] Generated log files remain excluded from Git

### Suggested Commit

```text
Add backend logging
```

---

# Epic 4 - Android Backend Integration

## [ ] ISSUE-011 Configure backend URL

### Description

Externalize server configuration for emulator and physical-device execution.

### Tasks

- [ ] Add emulator URL configuration
- [ ] Add physical-device URL configuration
- [ ] Create configuration abstraction
- [ ] Prevent local values from being committed unintentionally

### Acceptance Criteria

- [ ] Application can switch backend target without changing network code
- [ ] Emulator configuration supports the host alias `10.0.2.2`
- [ ] Physical-device configuration supports the Windows LAN IP address
- [ ] Local configuration is excluded from Git when necessary

### Suggested Commit

```text
Externalize backend configuration
```

---

## [ ] ISSUE-012 Add Android HTTP client

### Description

Create the Android network layer required to call the backend.

### Tasks

- [ ] Add HTTP client dependency
- [ ] Create backend service abstraction
- [ ] Implement `GET /health`
- [ ] Implement `GET /hello`
- [ ] Parse JSON responses
- [ ] Log request lifecycle without exposing sensitive information

### Acceptance Criteria

- [ ] Android client can call the health endpoint
- [ ] Android client can call the hello endpoint
- [ ] JSON responses are mapped to application models
- [ ] Network calls do not block the UI thread

### Suggested Commit

```text
Add Android HTTP client
```

---

## [ ] ISSUE-013 Connect UI to backend

### Description

Connect the user interface to the backend service and display the result.

### Tasks

- [ ] Connect the button action
- [ ] Display loading state
- [ ] Trigger the `/hello` request
- [ ] Parse the response
- [ ] Display connection status
- [ ] Display server message

### Acceptance Criteria

- [ ] Pressing Connect triggers one backend request
- [ ] A loading or connecting state is visible
- [ ] Successful response changes the status to `Connected`
- [ ] The server message is displayed

Example:

```text
Status:
Connected

Hello from server
```

### Suggested Commit

```text
Implement client server communication
```

---

## [ ] ISSUE-014 Handle connection failures

### Description

Implement visible and diagnosable connection error handling.

### Tasks

- [ ] Add connection timeout
- [ ] Handle unreachable backend
- [ ] Handle invalid response
- [ ] Display understandable error state
- [ ] Add retry behavior
- [ ] Log failure details

### Acceptance Criteria

- [ ] Application remains responsive when the backend is unavailable
- [ ] User receives understandable feedback
- [ ] Failure details are available in Logcat
- [ ] Retry can succeed after backend availability is restored

### Suggested Commit

```text
Add connection error handling
```

---

# Epic 5 - Emulator Validation

## [ ] ISSUE-015 Validate emulator workflow

### Description

Verify the complete client-server flow on the Android emulator.

### Tasks

- [ ] Start backend
- [ ] Start emulator
- [ ] Install and launch application
- [ ] Connect to backend
- [ ] Verify displayed response
- [ ] Capture relevant logs
- [ ] Record findings in `lessons_learned.md`

### Acceptance Criteria

- [ ] End-to-end flow works on emulator
- [ ] Emulator reaches the Windows-hosted backend
- [ ] Expected response appears in the application
- [ ] Android and backend logs allow the request to be traced

### Suggested Commit

```text
Document validated emulator workflow
```

---

# Epic 6 - Physical Device Validation

## [ ] ISSUE-016 Validate physical-device workflow

### Description

Execute and document the complete workflow on a physical Android device.

### Tasks

- [ ] Enable USB debugging
- [ ] Connect device
- [ ] Verify device with ADB
- [ ] Install APK
- [ ] Configure LAN backend address
- [ ] Verify Windows Firewall configuration if needed
- [ ] Connect to backend
- [ ] Compare behavior with emulator
- [ ] Record findings in `lessons_learned.md`

### Acceptance Criteria

- [ ] Device is visible through ADB
- [ ] APK installs and starts successfully
- [ ] End-to-end flow works on the physical device
- [ ] Relevant emulator/device differences are documented

### Suggested Commit

```text
Document validated physical device workflow
```

---

# Epic 7 - Appium Foundation

## [ ] ISSUE-017 Create Appium project

### Description

Create the basic Appium and pytest automation framework.

### Tasks

- [ ] Add Appium Python dependencies
- [ ] Add pytest configuration
- [ ] Create driver factory
- [ ] Add emulator capability configuration
- [ ] Add physical-device capability configuration
- [ ] Add test logging
- [ ] Validate an Appium session

### Acceptance Criteria

- [ ] Python dependencies install successfully
- [ ] Appium server starts successfully
- [ ] UiAutomator2 driver is available
- [ ] Appium session starts against the emulator
- [ ] Environment values are not hard-coded in tests

### Suggested Commit

```text
Create Appium framework skeleton
```

---

## [ ] ISSUE-018 Add Page Object Model

### Description

Create a maintainable abstraction for the main Android screen.

### Tasks

- [ ] Create `MainPage`
- [ ] Implement element locators
- [ ] Implement page readiness check
- [ ] Implement Connect action
- [ ] Implement status and response accessors
- [ ] Implement success and error waits

### Acceptance Criteria

- [ ] Tests contain no direct UI locators
- [ ] Page object uses stable identifiers
- [ ] Page object exposes user-relevant actions and states
- [ ] Fixed sleeps are not required

### Suggested Commit

```text
Add Appium page objects
```

---

## [ ] ISSUE-019 Implement successful connection test

### Description

Create the first automated end-to-end test for the successful connection flow.

### Tasks

- [ ] Verify backend readiness
- [ ] Launch or reset application
- [ ] Verify initial state
- [ ] Trigger connection
- [ ] Wait for success
- [ ] Verify status text
- [ ] Verify backend message

### Acceptance Criteria

- [ ] Test passes on emulator
- [ ] Test is independently executable
- [ ] Test uses page-object methods
- [ ] Test does not use fixed sleeps

### Suggested Commit

```text
Add happy path Appium test
```

---

## [ ] ISSUE-020 Implement failure and retry test

### Description

Test the backend-unavailable scenario and recovery through retry.

### Tasks

- [ ] Configure unavailable backend
- [ ] Launch application
- [ ] Trigger connection
- [ ] Verify visible error state
- [ ] Restore backend availability
- [ ] Trigger retry
- [ ] Verify successful recovery

### Acceptance Criteria

- [ ] Expected error state is displayed
- [ ] Application remains responsive
- [ ] Retry succeeds without reinstalling the application
- [ ] Failure and recovery are visible in logs

### Suggested Commit

```text
Add connection failure and retry test
```

---

# Epic 8 - Diagnostics and v0.1 Release

## [ ] ISSUE-021 Create automated diagnostic artifacts

### Description

Automatically collect debugging evidence when an Appium test fails.

### Tasks

- [ ] Generate session ID
- [ ] Capture screenshot
- [ ] Capture page source
- [ ] Capture filtered Logcat output
- [ ] Preserve Appium server log
- [ ] Preserve pytest log and stack trace
- [ ] Preserve relevant backend log excerpt
- [ ] Write environment metadata

### Acceptance Criteria

- [ ] Failed test creates a dedicated artifact directory
- [ ] Artifact directory contains screenshot and page source
- [ ] Relevant logs are available
- [ ] Environment metadata identifies emulator or physical device without exposing sensitive IDs
- [ ] Generated artifacts remain excluded from Git

### Suggested Commit

```text
Add automated test diagnostics
```

---

## [ ] ISSUE-022 Run Appium test on physical device

### Description

Run the automated happy-path test on a physical Android device using the same test logic.

### Tasks

- [ ] Select physical-device configuration
- [ ] Verify ADB connection
- [ ] Verify backend reachability
- [ ] Start Appium session
- [ ] Execute happy-path test
- [ ] Compare results with emulator execution
- [ ] Record differences

### Acceptance Criteria

- [ ] The same happy-path test passes on the physical device
- [ ] Only environment configuration differs from emulator execution
- [ ] No device-specific locator changes are required
- [ ] Observed differences are documented

### Suggested Commit

```text
Validate Appium test on physical device
```

---

## [ ] ISSUE-023 Complete v0.1 release verification

### Description

Execute the complete verification scope and prepare the first working sample.

### Tasks

- [ ] Run backend unit and API tests
- [ ] Build debug APK
- [ ] Run manual emulator check
- [ ] Run manual physical-device check
- [ ] Run Appium tests on emulator
- [ ] Run Appium happy path on physical device
- [ ] Demonstrate failure artifact creation
- [ ] Update documentation
- [ ] Update project status in `README.md`
- [ ] Review `lessons_learned.md`

### Acceptance Criteria

- [ ] All mandatory tests pass
- [ ] APK builds successfully from a clean checkout
- [ ] Backend setup is reproducible from documentation
- [ ] Emulator workflow is documented and functional
- [ ] Physical-device workflow is documented and functional
- [ ] Appium happy-path test passes on both targets
- [ ] Negative connection test passes on the emulator
- [ ] Failure artifacts are generated correctly
- [ ] Known limitations are documented

### Suggested Commit

```text
Prepare version 0.1 release
```

---

## [ ] ISSUE-024 Tag version v0.1

### Description

Create the first version tag after all release criteria have been met.

### Tasks

- [ ] Confirm clean Git working tree
- [ ] Confirm release verification is complete
- [ ] Create annotated Git tag
- [ ] Verify tag locally

### Acceptance Criteria

- [ ] Working tree is clean
- [ ] All v0.1 Definition of Done items are complete
- [ ] Annotated `v0.1` tag exists

Example:

```powershell
git tag -a v0.1 -m "First working Android client-server Appium sample"
git tag --list
```

### Suggested Commit

No additional source commit should be required solely for the tag. If documentation changes are necessary, commit them before creating the tag.

---

# Definition of Done for v0.1

- [ ] APK builds successfully
- [ ] Backend runs locally from documented setup steps
- [ ] `/health` and `/hello` work as specified
- [ ] Android client displays the backend response
- [ ] Connection failures are handled visibly
- [ ] Structured logging is implemented
- [ ] Emulator execution is verified
- [ ] Physical-device execution is verified
- [ ] Appium framework is implemented
- [ ] Happy-path Appium test passes on emulator
- [ ] Happy-path Appium test passes on physical device
- [ ] Failure-and-retry Appium test passes on emulator
- [ ] Failure artifacts are collected automatically
- [ ] Documentation reflects the implemented solution
- [ ] Known limitations and lessons are recorded
- [ ] Version is tagged as `v0.1`

---

# Recommended Execution Order

Work through the issues in numerical order. Each issue should result in one focused commit wherever practical.

```text
Repository
    -> Development environment
    -> Android foundation
    -> Backend foundation
    -> Client-server integration
    -> Emulator validation
    -> Physical-device validation
    -> Appium foundation
    -> Automated scenarios
    -> Diagnostics
    -> v0.1 verification and tag
```
