---
description: >-
  Add BugSplat Native 9.0 to a macOS application: the out-of-process monitor and
  reporter inside your bundle, signing and notarization, .sym symbols.
---

# macOS

{% hint style="warning" %}
**Status: bring-up.** The macOS build compiles and passes the unit tests in CI; the crash-through-dialog path is being verified on hardware, and the AppKit reporter is being finished. This page describes the design that ships; details marked *planned* are not yet in a release. For production today use [BugSplat for macOS](../desktop/macos.md) (bugsplat-apple 2.x), which will be replaced by bugsplat-apple 9.0 on this core.
{% endhint %}

### Requirements 📋

* macOS 13 or later, Apple silicon and Intel (universal binaries).
* Xcode 16 or later to build; CMake 3.24+ for the CMake package.
* Hardened runtime and notarization for anything you distribute outside the App Store; see below.

### What ships inside your bundle 📦

```
MyApp.app/Contents/
  MacOS/MyApp
  Frameworks/libbugsplat.dylib            (or BugSplat.framework)
  Helpers/BugSplatMonitor                 out-of-process capture
  Helpers/BugSplatReporter.app            the crash dialog and upload
```

`bugsplat_install_runtime(TARGET my_app)` puts the helpers in `Contents/Helpers` for a `MACOSX_BUNDLE` target (next to the executable for a command-line tool) and, when `BUGSPLAT_CODESIGN_IDENTITY` is set, signs `BugSplatMonitor` with the hardened runtime. `bugsplat_init()` also looks in `BugSplat.framework/Helpers/` so the Swift package can carry the helpers itself.

### Initialize 🏗️

```c
bugsplat_options* o = bugsplat_options_new("fred", "MyApp", "1.0.0");
bugsplat_options_set_upload_policy(o, BUGSPLAT_UPLOAD_DIALOG);
bugsplat_options_set_hang_detection(o, 3000, BUGSPLAT_HANG_REPORT);
if (bugsplat_init(o) != BUGSPLAT_OK) { /* helpers missing from the bundle */ }
```

The C++ wrapper and the report properties, attachments, feedback, structured reports and pending-report calls are the same as on [Windows](windows.md); the Swift-first API arrives with bugsplat-apple 9.0.

### What is different on macOS 🍎

* **Crash callback**: there is no in-process crash callback on Apple platforms (`BUGSPLAT_CAP_ON_CRASH_CALLBACK` is 0). Put state into attributes ahead of time.
* **Hang detection** pings the main dispatch queue; a run loop that stops turning is a hang. Command-line tools that block on stdin should call `bugsplat_heartbeat()` or leave detection off.
* **Heap and full dumps** are standard minidumps with the process's memory regions appended, readable by `lldb` and `minidump_stackwalk`.
* **Support response** opens with `open <infoUrl>` after an interactive upload.
* **App Sandbox**: an out-of-process handshake inside the sandbox is being validated. If no sandbox-compatible mechanism passes App Review, sandboxed apps fall back automatically to in-process capture with the same store, reporter and upload (*planned*; see the architecture document's risk register).

### Signing and notarization 🔏

Sign `BugSplatMonitor`, `BugSplatReporter.app` and `libbugsplat.dylib` with your Developer ID and the hardened runtime as part of your app's signing, then notarize the app as usual. The helpers are plain executables and need no entitlements of their own; the app needs `Outgoing network connections (client)` only when it is sandboxed.

### Symbols 🔣

macOS reports are processed with Breakpad `.sym` files; dSYMs are the input. Set `DEBUG_INFORMATION_FORMAT = dwarf-with-dsym` for release builds, keep Bitcode off, and after every build:

```bash
symbol-upload-macos -b your-database -a MyApp -v 1.0.0 -i your-client-id -s your-client-secret -d "$BUILT_PRODUCTS_DIR" -f "**/*.dSYM" -m
```

`-m` runs `dump_syms` and uploads the `.sym`. See [Upload Symbols with symbol-upload](../../../development/working-with-symbol-files/upload-symbols-with-symbol-upload.md).

### Where things are 🔍

Reports and `BugSplat.log`: `~/Library/Application Support/BugSplat/<app>-<version>/`. The dialog's remembered name and email are in `preferences.json` there.

### Troubleshooting 🛠️

| Symptom | Cause |
| --- | --- |
| `BUGSPLAT_ERR_MONITOR_NOT_FOUND` | `Contents/Helpers/BugSplatMonitor` is missing, or the target is not a bundle and the helper is not next to the executable |
| Gatekeeper blocks the helper | the helper was not signed with the app's identity, or the app was not notarized after the helpers were added |
| No symbols on the crash page | `.sym` files were not uploaded for this application and version (upload the dSYMs with `-m`) |
