# Unity on macOS

BugSplat for Unity captures native crashes on macOS with [bugsplat-apple](https://github.com/BugSplat-Git/bugsplat-apple), which uses PLCrashReporter to capture crashes via Mach exception handling. Native crash reporting (`UseNativeCrashReportingForMac`) is on by default and requires the **IL2CPP** scripting backend. A Mono player logs a warning at build time and at startup, and reports managed exceptions only.

Crashes are captured at crash time and uploaded on the next launch. By default the user is asked before a crash report is sent; set `MacAutoSubmitCrashReport` to `true` on your options asset to send them without asking.

When native crash reporting is enabled, the post-build step also copies `bugsplat-logo.png` into the built player's `Contents/Resources` so the crash dialog can show the BugSplat logo. Xcode project exports are skipped — add the file to your Xcode target's resources yourself if you want the logo there.

### macOS Symbols

To upload symbols, set `UploadDebugSymbolsForMac` to `true` on your options asset and provide [symbol upload credentials](symbol-upload.md). For IL2CPP builds, BugSplat uploads dSYMs and `LineNumberMappings.json` for full symbolication. The upload is skipped when the build is an Xcode project export.

### Player Log

`Player.log` is attached to native macOS crash reports when both `UseNativeCrashReportingForMac` and `CapturePlayerLog` are enabled. Managed exception reports attach it through the managed reporter instead, so they are unaffected by the native setting.

Because a native crash report uploads at the next launch, by which time Unity has renamed the crashed session's log to `Player-prev.log`, BugSplat attaches `Player-prev.log` to that report. If the app crashes again before BugSplat initializes, Unity's log rotation has already replaced that file, and the report carries no `Player.log` rather than a misleading one.

### Attachments

A native crash report is uploaded at the **next launch**, not at crash time, and BugSplat asks for its attachments then — in a fresh process that did not experience the crash. A file registered with `AttachNativeLogFile` part-way through a session is therefore not remembered across the crash and never reaches the report.

Register native attachments during initialization instead. `PersistentDataFileAttachmentPaths` on your options asset is applied on every launch and is unaffected by this. Each attachment is truncated to its last 10 MB. See [Attaching Files to Native Crash Reports](api.md#attaching-files-to-native-crash-reports).

### Hang Detection

When `UseNativeCrashReportingForMac` is enabled, BugSplat also detects fatal main-thread hangs. No additional configuration is required. If the main thread stalls past the detection threshold and the app is subsequently terminated without recovering, BugSplat uploads an `App Hang (Fatal)` report on the next launch. Hangs the app recovers from are not reported.

The detection threshold is `MacHangDetectionThresholdSeconds`, 5 seconds by default. A Unity game routinely blocks the main thread for seconds at a time — scene loads, shader warmup, synchronous asset loads — so lower it only if your game genuinely never stalls that long.

By default a hang report is uploaded without asking, because the user never had the chance to consent — the app was frozen, then terminated. Turn off `MacAutoSubmitFatalHangReport` to ask them instead: the report then goes through the same dialog a native crash shows, so they can describe what the app was doing when it froze. That also needs `MacAutoSubmitCrashReport` off, since it decides whether any dialog appears.

{% hint style="info" %}
Unlike iOS, macOS has no OS watchdog that terminates an unresponsive app — it beachballs indefinitely — so a hang only becomes fatal when the user force-quits (Option-Command-Escape, Activity Monitor, or a `kill`). Detection is also suppressed while a debugger is attached, so test hang reporting on a built player run outside Xcode.
{% endhint %}
