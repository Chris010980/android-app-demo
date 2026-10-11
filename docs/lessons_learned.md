# Lessons Learned

## 1. Purpose

This document records practical findings from implementing and testing the Android client, backend, and Appium automation. It is not a daily activity log. Each entry should capture knowledge that will improve future technical decisions, debugging, repeatability, or test quality.

The document is intentionally created before implementation so that useful observations are recorded while they are still fresh.

## 2. What to Record

Suitable topics include:

- differences between emulator and physical-device behavior,
- Android SDK, Gradle, Kotlin, or dependency compatibility issues,
- network addressing and Windows Firewall behavior,
- Android permissions and clear-text HTTP restrictions,
- reliable and unreliable Appium locator strategies,
- timing, synchronization, and explicit-wait behavior,
- root causes of failed builds or tests,
- effective Logcat filters and diagnostic commands,
- useful combinations of Android, backend, Appium, and test logs,
- setup steps that were missing or misleading,
- design choices that improved testability,
- technical debt intentionally accepted for the learning project,
- changes needed before CI or wider distribution.

Avoid storing passwords, access tokens, private device identifiers, personal data, or unsanitized production logs.

## 3. Entry Template

Copy the following template for each significant lesson:

```markdown
## YYYY-MM-DD - Short descriptive title

### Context
What was being implemented or tested?

### Observation
What happened? Include relevant environment information without exposing secrets.

### Root Cause
What caused the behavior? If the cause is not confirmed, clearly mark it as a hypothesis.

### Resolution
What change or command solved the problem?

### Evidence
Reference relevant log files, error messages, screenshots, commits, or documentation.

### Impact
Did this affect the emulator, physical device, backend, Appium tests, or all environments?

### Follow-up
What should be automated, documented, refactored, or tested next?
```

## 4. Decision Record Template

Use this shorter format when the lesson is primarily an architectural or tooling decision:

```markdown
## YYYY-MM-DD - Decision title

- **Decision:** What was selected?
- **Reason:** Why was it selected?
- **Alternatives considered:** What else was evaluated?
- **Trade-offs:** What limitations were accepted?
- **Review trigger:** Under which condition should the decision be reconsidered?
```

## 5. Suggested Initial Categories

### Environment Setup

Record installation, path, SDK, driver, and version compatibility findings.

### Emulator vs. Physical Device

Document observed differences in networking, performance, permissions, rendering, lifecycle behavior, and test stability.

### Android Application

Capture UI-state, lifecycle, networking, error-handling, and logging lessons.

### Backend

Capture endpoint design, configuration, request tracing, error handling, and local network lessons.

### Appium Automation

Capture locator quality, waits, capabilities, reset behavior, session reliability, and artifact collection.

### Logging and Diagnostics

Record which logs were useful, how events were correlated, and which data was unnecessarily noisy or missing.

### Git and Repository Management

Record ignore-rule corrections, branching decisions, accidental generated files, and repository-layout changes.

## 6. Current Lessons

No implementation lessons have been recorded yet.
