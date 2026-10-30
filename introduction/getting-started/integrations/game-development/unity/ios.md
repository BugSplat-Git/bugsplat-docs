# Unity on iOS

BugSplat for Unity captures native crashes on iOS with [bugsplat-apple](https://github.com/BugSplat-Git/bugsplat-apple), which uses PLCrashReporter to capture crashes via Mach exception handling. Native crash reporting (`UseNativeCrashReportingForIos`) is on by default.

When native crash reporting is enabled, BugSplat automatically disables Unity's built-in crash reporter during the build to prevent conflicts with PLCrashReporter. Crashes are captured at crash time and uploaded on the next app launch. Crash reports are sent without asking the user by default; set `IosAutoSubmitCrashReport` to `false` on your options asset to show the BugSplat dialog on the next launch instead.

`Player.log` is attached to native iOS crash reports when both `UseNativeCrashReportingForIos` and `CapturePlayerLog` are enabled. Managed exception reports attach it through the managed reporter instead, so they are unaffected by the native setting.

### iOS Symbols

To upload symbols, set `UploadDebugSymbolsForIos` to `true` on your options asset and provide [symbol upload credentials](symbol-upload.md). BugSplat adds a build phase to the generated Xcode project that uploads dSYMs during the Xcode build. For IL2CPP builds, it also uploads `LineNumberMappings.json`, which maps IL2CPP-generated C++ symbols back to the original C# method names, file names, and line numbers.

When credentials are missing, the upload is skipped with an Xcode build warning and the build still succeeds.

### Attachments

A native crash report is uploaded at the **next launch**, not at crash time, and BugSplat asks for its attachments then — in a fresh process that did not experience the crash. A file registered with `AttachNativeLogFile` part-way through a session is therefore not remembered across the crash and never reaches the report.

Register native attachments during initialization instead. `PersistentDataFileAttachmentPaths` on your options asset is applied on every launch and is unaffected by this. Each attachment is truncated to its last 10 MB. See [Attaching Files to Native Crash Reports](api.md#attaching-files-to-native-crash-reports).

### Hang Detection

When `UseNativeCrashReportingForIos` is enabled, BugSplat also detects fatal main-thread hangs. No additional configuration is required. If the main thread stalls past the detection threshold (`IosHangDetectionThresholdSeconds`, 5 seconds by default) and the app is subsequently terminated without recovering — by the OS watchdog at launch or resume, or by the user force-quitting — BugSplat uploads an `App Hang (Fatal)` report on the next launch. Hangs the app recovers from are not reported.

Hang reports are uploaded without asking by default. Turn off `IosAutoSubmitFatalHangReport` (and `IosAutoSubmitCrashReport`) to route them through the crash dialog instead.

{% hint style="info" %}
Hang detection is suppressed while a debugger is attached. Test it on a build run without the Xcode debugger.
{% endhint %}
