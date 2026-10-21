# Vision-Guard

A privacy-first Android app that protects your eyesight, your screen privacy and your time, using on-device face detection and local usage tracking. Nothing leaves the phone: the app declares no `INTERNET` or `ACCESS_NETWORK_STATE` permission.

> **Status:** prototype built for a product-development competition. Phases 0–4 are implemented; some physical-device checks are still **UNVERIFIED**. See [STATUS.md](STATUS.md) for exactly what has and hasn't been verified.

## Features

| | Feature | What it does |
|---|---|---|
| F1 | **Eye Guard** | Estimates how far the phone is from your face and dims the screen with a friendly warning when it's too close. |
| F2 | **Privacy Guard** | Detects a second viewer looking at the screen and shows a frosted shield overlay (with blur on Android 12+). |
| F3 | **Profiles** | App-level Parent/Child profiles. Switching profiles requires a Parent PIN (PBKDF2-HMAC-SHA256, with persistent lockout after failed attempts). |
| F4 | **Smart Dashboard** | Today's screen time, 7-day trend, top apps, protection counts, and rule-based wellbeing suggestions. |
| F5 | **Time Tokens** | *Planned (Phase 5).* Limits, cooldowns and a 5-minute grace window. |

## How it works

- **Vision:** CameraX + ML Kit Face Detection (bundled model, works offline). Distance uses a pinhole estimate calibrated once at ~30 cm.
- **Policy:** decision logic lives in a pure-Kotlin `policy` package with no Android imports, so it is unit-tested on the JVM.
- **Usage:** foreground time comes from `UsageStatsManager`, read locally.
- **Storage:** Room for events; app-private preferences for PIN and profile state.
- **UI:** Jetpack Compose + Material 3, state-based bottom navigation, no DI framework.

Package tour: [CODE_TOUR.md](CODE_TOUR.md).

## Honest limits

- Distance is approximate (target is about 15% error after calibration).
- Some phone brands may kill background services.
- Profiles are app-level, not OS-level accounts.
- Battery drain has not been measured.

## Requirements

- Android Studio, with JDK 21 and the Android SDK (compile/target SDK 35)
- Git
- A physical Android device (Android 10 / API 29 or newer) with USB debugging enabled, or an emulator. A real phone with a front camera is needed to try face-based features.

## Getting started

```bash
git clone https://github.com/Hrushikesh-Mysore/Vision-Guard.git
cd Vision-Guard
chmod +x gradlew
./gradlew installDebug
```

Then open **VisionGuard** on the device. On first run, grant the permissions listed in the checklist (camera, overlay, usage access) and complete the one-tap calibration.

### Build only

```bash
./gradlew assembleDebug
# APK: app/build/outputs/apk/debug/app-debug.apk
adb install -r app/build/outputs/apk/debug/app-debug.apk
```

### Tests and manifest check

```bash
./gradlew test
./gradlew :app:processDebugMainManifest
```

Inspect the merged manifest and confirm `INTERNET` and `ACCESS_NETWORK_STATE` are absent.

## Troubleshooting

- **Device not detected:** run `adb devices`. If it shows `unauthorized`, unlock the phone and accept the USB debugging prompt. If ADB hangs: `adb kill-server && adb start-server`.
- **`gradlew: Permission denied`:** `chmod +x gradlew`.
- **Stale build output:** `./gradlew clean assembleDebug`.
- **Dashboard is empty:** grant *Usage access* to VisionGuard in system settings.

## Project docs

| File | Purpose |
|---|---|
| [STATUS.md](STATUS.md) | Feature-by-feature status and how each was verified |
| [CHANGELOG.md](CHANGELOG.md) | History of changes |
| [CODE_TOUR.md](CODE_TOUR.md) | Plain-English tour of each package |
| [HANDOFF.md](HANDOFF.md) | Current state for the next contributor |
| [AGENTS.md](AGENTS.md) | Working agreement for AI-assisted development |
| [docs/](docs) | Software Requirements Specification |
