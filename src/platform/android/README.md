# mGBA for Android (SDL2 port)

A standalone, installable APK build of mGBA for Android, built on the SDL
frontend with a touch-first UI added for phones and tablets.

This is the first working Android target in this repo — upstream mgba has no
Android platform (the Play Store version is built from a separate codebase).

## What you get

- `com.gitsnup.mgbaandroid` — arm64-v8a APK, minSdk 21 (Android 5.0+)
- ROM browser at launch (touch or gamepad/keyboard)
- On-screen gamepad overlay during gameplay: D-pad, A/B, L/R, Start/Select,
  plus a toggle button (top right) to show/hide the overlay
- Letterboxed, aspect-correct video
- Physical gamepads and keyboards work through SDL's game controller API
- Save states, save games and config stored in the app's private storage

## Where to put ROMs

The ROM browser scans (in order):

1. The app's external files dir:
   `Android/data/com.gitsnup.mgbaandroid/files/` (no permissions needed)
2. `/sdcard/ROMS`
3. `/sdcard/Download`

Supported extensions: `.gba`, `.agb`, `.gb`, `.gbc`, `.sgb`, `.cgb`, `.zip`.

## Building from source

Prerequisites: JDK 17, Android SDK (platform 34, build-tools 34, NDK
26.3.11579264, cmake 3.22.1), Gradle 8.5+, and the SDL2 source tree
(2.30.x).

```sh
cd src/platform/android
SDL2_SRC=/path/to/SDL2 ./build-apk.sh --release
```

The APK lands in `app/build/outputs/apk/release/`. For a signed release,
create `keystore.properties` (gitignored) next to `build-apk.sh`:

```properties
storeFile=/absolute/path/to/keystore
storePassword=...
keyAlias=...
keyPassword=...
```

## How it works

- The mGBA core is built as a static library (`LIBMGBA_ONLY`).
- SDL2 is built as a static library from `SDL2_SRC` and provides the Android
  activity glue (`SDLActivity` Java classes are vendored under `app/`).
- `android-main.c` is an adapted copy of the SDL frontend `main.c`:
  it adds the ROM browser, a letterboxed touch-aware run loop, and points
  mGBA's config/save directory at the app's private storage via
  `XDG_CONFIG_HOME`.
- `android-ui.c` implements the ROM browser and the touch overlay, and
  synthesizes standard keyboard events so all existing key bindings and
  hotkeys keep working.
