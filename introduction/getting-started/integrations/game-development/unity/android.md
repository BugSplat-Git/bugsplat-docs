# Unity on Android

BugSplat for Unity captures native C++ crashes on Android via Crashpad. Native crash reporting (`UseNativeCrashReportingForAndroid`) is on by default.

### Player Settings

Configure the scripting backend to use **IL2CPP**, target **ARM64**, and set the Minimum API Level to **Android 8.0 (API level 26)** or higher. ARM64 is the only supported architecture; other ABIs are untested and unsupported.

<figure><img src="../../../../../.gitbook/assets/image (70).png" alt="Android Player Settings with IL2CPP and ARM64 selected"><figcaption></figcaption></figure>

### Android Symbols

To upload symbols, set `UploadDebugSymbolsForAndroid` to `true` on your options asset and provide [symbol upload credentials](symbol-upload.md). When you build your app, set **Create symbols.zip** to **Debugging**. BugSplat uploads the generated symbols archive after the build; the upload is skipped when **Export Project** is enabled or **Debug Symbols** is **None**.

<figure><img src="../../../../../.gitbook/assets/image (71).png" alt="Android Build Settings with Create symbols.zip set to Debugging"><figcaption></figcaption></figure>

### Attachments

With `UseNativeCrashReportingForAndroid` enabled, paths listed in `PersistentDataFileAttachmentPaths` are resolved against `Application.persistentDataPath` and registered with the native crash reporter at startup, so they ride along with native Android crash reports as well as managed ones.

`AttachNativeLogFile` and `DetachNativeLogFile` work on Android as they do on the other platforms. Files are read when a crash is uploaded, so a log can be attached before anything has written to it. See [Attaching Files to Native Crash Reports](api.md#attaching-files-to-native-crash-reports).

Unity does not write a `Player.log` on Android — log output goes to logcat — so `CapturePlayerLog` has no effect on Android reports.

### ANR Reporting

When `UseNativeCrashReportingForAndroid` is enabled, BugSplat also reports ANRs (Application Not Responding events). No additional configuration is required. On the next launch, BugSplat queries the OS for ANRs that occurred during the previous session and uploads them as `Android.ANR` reports.

{% hint style="info" %}
ANR reporting requires Android 11 (API level 30) or higher at runtime. On older OS versions it is silently skipped while native crash reporting continues to work.
{% endhint %}
