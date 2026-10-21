# Android app (Empty Views Activity — Java + XML)

This repository originally contained only Python scripts (`grabber.py`, `server.py`).
A standard Android **Empty Views Activity** project (Java + XML layouts, no Compose)
has been scaffolded alongside them, so the project root is now also a valid
Gradle/Android project root containing `gradlew` / `gradlew.bat`.
The pre-existing Python files are untouched.

## Pinned versions

| Component | Version |
|---|---|
| Gradle (wrapper) | 8.7 (`gradle-8.7-bin.zip`) |
| Android Gradle Plugin | 8.5.2 |
| JDK | 17 (required to run AGP 8.5) |
| compileSdk / targetSdk | 34 |
| minSdk | 24 |
| Java language level | 1.8 |
| AppCompat | 1.7.0 |
| Material | 1.12.0 |
| ConstraintLayout | 2.1.4 |

* `applicationId` / namespace: `com.example.emptyviewsapp`
* App module: `app/` — `MainActivity.java`, `res/layout/activity_main.xml` ("Hello World!")

The Gradle wrapper JAR was sourced from the official `gradle/gradle` repository
(tag `v8.7.0`). Its SHA-256 is
`cb0da6751c2b753a16ac168bb354870ebb1e162e9083f116729cec9c781156b8`, which matches
the checksum registered for Gradle 8.7 in `gradle/actions`'
`wrapper-checksums.json`; CI additionally runs
`gradle/actions/wrapper-validation` on every build.

## Build the debug APK (no device needed)

Requirements: JDK 17 and an Android SDK (`ANDROID_HOME`, or `sdk.dir` in
`local.properties`). Opening the project in Android Studio installs/points to
both automatically.

```text
# Windows (cmd / PowerShell, from this directory)
gradlew.bat assembleDebug

# Linux / macOS
./gradlew assembleDebug
```

Output APK: `app/build/outputs/apk/debug/app-debug.apk`

## Install on a device/emulator — `installDebug`

`installDebug` = `assembleDebug` + `adb install`, so it additionally needs a
running emulator or a USB device with **USB debugging** enabled:

```text
adb devices              :: must list your device as "device", not "unauthorized"
gradlew.bat installDebug
```

(Linux/macOS equivalent: `./gradlew installDebug`.) This step cannot run in a
headless sandbox/CI without an emulator, which is why CI below builds the APK
instead of installing it — installing that APK is exactly the second half of
what `installDebug` does on your machine.

## CI — GitHub Actions

`.github/workflows/android-assemble-debug.yml` runs on every push to
`master` / `arena/**` and on pull requests:

1. validates the Gradle wrapper,
2. sets up Temurin JDK 17 (the runner image provides the Android SDK),
3. runs `./gradlew testDebugUnitTest`,
4. runs `./gradlew assembleDebug`,
5. uploads **`app-debug-apk`** (`app-debug.apk`) as a workflow artifact
   (Actions tab → run → Artifacts).

## Download a ready-to-install APK (GitHub Releases)

**Direct download — `EmptyViewsApp-v1.0.apk`:**
https://github.com/mauespinoza/whatsapp-hacking/releases/download/v1.0/EmptyViewsApp-v1.0.apk

All published versions: https://github.com/mauespinoza/whatsapp-hacking/releases

Download the APK on your phone (Android 7.0 / API 24+), open it, allow
"install unknown apps" for your browser/file manager if Android asks, then
**Install → Open**. The APK is debug-signed, so it installs on a device as-is
(it is not a Google Play release build). Each release also attaches
`SHA256SUMS.txt` to verify the download.

To publish a new version: bump `versionCode`/`versionName` in
`app/build.gradle`, commit, then push a `vX.Y` tag —
`.github/workflows/release-apk.yml` rebuilds and attaches
`EmptyViewsApp-vX.Y.apk` to a new GitHub Release automatically.

## Other useful tasks

```text
gradlew.bat testDebugUnitTest   :: host-side JUnit tests
gradlew.bat connectedDebugAndroidTest  :: instrumented tests (needs device/emulator)
gradlew.bat clean
```
