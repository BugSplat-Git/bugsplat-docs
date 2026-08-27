# Unity

### 🏗 Installation

BugSplat's `com.bugsplat.unity` package can be added to your project via [OpenUPM](https://openupm.com/packages/com.bugsplat.unity/) or a URL to our git [repository](https://github.com/BugSplat-Git/bugsplat-unity.git).

#### OpenUPM

Information on installing OpenUPM can be found [here](https://openupm.com/). After installing OpenUPM, run the following command to add BugSplat to your project.

```
openupm add com.bugsplat.unity
```

#### Git

Information on adding a Unity package via a git URL can be found [here](https://docs.unity3d.com/Manual/upm-ui-giturl.html).

```
https://github.com/BugSplat-Git/bugsplat-unity.git
```

### 🧑‍🏫 Sample

{% hint style="success" %}
BugSplat recommends building with the IL2CPP backend for the best crash reporting experience. For more information, please see the [Player Settings](#player-settings) section.
{% endhint %}

After installing `com.bugsplat.unity`, you can import a sample project to help you get started with BugSplat. Click here if you'd like to skip the sample project and get straight to the [usage](#usage) instructions.

To import the sample, click the caret next to **Samples** to reveal the **my-unity-crasher** sample. Click **Import** to add the sample to your project.

<figure><img src="../../../../.gitbook/assets/image (58).png" alt=""><figcaption></figcaption></figure>

In the Project Assets browser, open the **Sample** scene from `Samples > BugSplat > Version > my-unity-crasher > Scenes`.

Next, select `Samples > BugSplat > Version > my-unity-crasher` to reveal the **BugSplatOptions** object. Click BugSplatOptions and replace the database value with your BugSplat database.

<figure><img src="../../../../.gitbook/assets/image (59).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (60).png" alt=""><figcaption></figcaption></figure>

Click **Play** and click or tap one of the buttons to send an error report to BugSplat. To view the error report, navigate to the BugSplat [Dashboard](https://app.bugsplat.com/v2/dashboard) and ensure you have selected the correct database.

<figure><img src="../../../../.gitbook/assets/image (61).png" alt=""><figcaption></figcaption></figure>

Navigate to the [Crashes](https://app.bugsplat.com/v2/crashes) page, and click the value in the ID column to see the details of your report, including the call stack, log file, and screenshot of your app when the error occurred.

<figure><img src="../../../../.gitbook/assets/image (62).png" alt=""><figcaption></figcaption></figure>

### 🧰 Player Settings

For best results, BugSplat recommends building with the `IL2CPP` backend. The `Mono` backend is supported, but has several limitations. With `IL2CPP`, BugSplat can capture fully symbolicated C# exception traces in production, as well as native crashes that contain call stacks mapped back to their original C# function names, file names, and line numbers.

To optimize your game for crash reporting, open `Player Settings` (`Edit > Player Settings`). Navigate to the `Configuration` section. For `Scripting Backend` choose `IL2CPP` and for `IL2CPP StackTrace Information` choose `Method Name, File Name, and Line Number`.

<figure><img src="../../../../.gitbook/assets/image (63).png" alt=""><figcaption></figcaption></figure>

### ⚙️ Configuration

BugSplat's Unity integration is flexible and can be used in various ways. The easiest way to get started is to attach the `BugSplatManager` Monobehaviour to a GameObject.

<figure><img src="../../../../.gitbook/assets/image (64).png" alt=""><figcaption></figcaption></figure>

`BugSplatManager` needs to be initialized with a `BugSplatOptions` serialized object. A new instance of `BugSplatOptions` can be created through the Asset Create menu.

<figure><img src="../../../../.gitbook/assets/image (65).png" alt=""><figcaption></figcaption></figure>

Configure fields as appropriate. Note that if Application or Version are left empty, `BugSplat` will default these values to `Application.productName` and `Application.version`, respectively.

<figure><img src="../../../../.gitbook/assets/image (66).png" alt=""><figcaption></figcaption></figure>

Finally, provide a valid `BugSplatOptions` to `BugSplatManager`.

<figure><img src="../../../../.gitbook/assets/image (67).png" alt=""><figcaption></figcaption></figure>

### 🧭 Platform Support

What BugSplat captures on each platform. Setup for each one is covered in [🤖 Android](#android), [🍎 iOS](#ios), [🖥 macOS](#macos), and [🪟 Windows](#windows).

| Capability                      | Windows              | macOS             | iOS | Android           | Linux | WebGL |
| ------------------------------- | -------------------- | ----------------- | --- | ----------------- | ----- | ----- |
| Managed C# exceptions           | Yes                  | Yes               | Yes | Yes               | Yes   | Yes   |
| Native crashes                  | Yes (Mono or IL2CPP) | Yes (IL2CPP only) | Yes | Yes               | No    | No    |
| Hang / ANR reporting            | Yes (opt-in)         | No                | Yes | Yes (Android 11+) | No    | No    |
| Offline retry of native reports | Yes                  | Yes               | Yes | Yes               | n/a   | n/a   |
| User feedback (`PostFeedback`)  | Yes                  | Yes               | Yes | Yes               | Yes   | No    |
| Automatic symbol upload         | Yes                  | Yes               | Yes | Yes               | No    | No    |

* **Managed C# exceptions** are captured on every platform through Unity's log callbacks — including [background threads](#background-thread-exceptions) — and posted over HTTPS. WebGL uses a separate reporter that cannot attach log files or screenshots.
* **Native crashes** require the matching option on your `BugSplatOptions` asset: `UseNativeCrashReportingForWindows`, `UseNativeCrashReportingForMac`, `UseNativeCrashReportingForIos`, or `UseNativeCrashReportingForAndroid`. Linux and WebGL have no native reporter and fall back to managed exception reporting alone. Every native reporter is compiled out of the editor, so play mode exercises the managed rows only.
* **Hang / ANR reporting** is opt-in on Windows through `WindowsHangDetectionTimeoutMs` (`0`, disabled, by default) and automatic on iOS and Android once native crash reporting is enabled. Android ANRs additionally need Android 11 (API level 30) at runtime. macOS has no hang detection.
* **Offline retry** covers native reports only: they are written to disk when the crash happens and uploaded on a later launch, so being offline at crash time does not lose the report. Managed exception posts are never persisted — if that upload fails, the report is gone.
* **User feedback** is posted with `bugsplat.PostFeedback`. WebGL has no feedback client and logs an error instead.
* **Automatic symbol upload** runs as a post-build step and needs [symbol upload credentials](#symbol-upload). Windows uploads `.pdb`, `.dll`, and `.exe` files, and needs **Copy PDB files** enabled in **Build Settings > Windows** — without it the build contains no `.pdb` files at all. BugSplat skips the upload when it can see that setting is off, which it can only read from a Windows editor. macOS uploads dSYMs when `UploadDebugSymbolsForMac` is set, unless the build is an Xcode project export, where no dSYMs exist yet. iOS adds an Xcode build phase that uploads dSYMs during the Xcode build when `UploadDebugSymbolsForIos` is set. Android uploads the generated symbols archive when `UploadDebugSymbolsForAndroid` is set, and skips it when **Export Project** is enabled or **Debug Symbols** is **None**. Linux and WebGL have no symbol upload step.

Two things don't fit the table. `Post(FileInfo minidump)` works on every platform except WebGL, where it logs that it isn't implemented and returns without uploading. And IL2CPP's `LineNumberMappings.json`, which maps generated C++ frames back to C# names, files, and line numbers, is uploaded on Windows, macOS, and iOS only — the Android symbol upload sends native `.so` symbols alone.

{% hint style="warning" %}
`Utils.ForceCrash` goes through Unity's internal crash pipeline and is **not** captured by the native crash reporters on iOS, macOS, or Android. Test those platforms with a real native crash, such as a null pointer dereference in native code, which is what the sample's native scenarios use.
{% endhint %}

### ⌨️ Usage

If you're using `BugSplatOptions` and `BugSplatManager`, BugSplat automatically configures an `Application.logMessageReceived` handler that will post reports when it encounters a log message of type `Exception`. Unhandled exceptions raised on [background threads](#background-thread-exceptions) and inside [unobserved tasks](#unobserved-task-exceptions) are captured too. You can also extend your BugSplat integration and [customize report metadata](#adding-metadata), [report exceptions in try/catch blocks](#trycatch-reporting), [prevent repeated reports](#preventing-repeated-reports), [attach files to native crash reports](#attaching-files-to-native-crash-reports), and [capture native crashes on Windows](#windows).

#### Adding Metadata

First, find your instance of `BugSplat`. The following is an example of how to find an instance of `BugSplat` via `BugSplatManager`:

```csharp
var bugsplat = FindObjectOfType<BugSplatManager>().BugSplat;
```

You can extend `BugSplat` by setting the following properties:

```csharp
bugsplat.Attachments.Add(new FileInfo("/path/to/attachment.txt"));
bugsplat.Description = "description!";
bugsplat.Email = "fred@bugsplat.com";
bugsplat.Key = "key!";
bugsplat.Notes = "notes!";
bugsplat.User = "Fred";
bugsplat.CaptureEditorLog = true;
bugsplat.CapturePlayerLog = false;
bugsplat.CaptureScreenshots = true;
```

You can use the `Notes` field to capture arbitrary data such as system information:

```csharp
void Start()
{
    bugsplat = FindObjectOfType<BugSplatManager>().BugSplat;
    bugsplat.Notes = GetSystemInfo();
}

private string GetSystemInfo()
{
    var info = new Dictionary<string, string>();
    info.Add("OS", SystemInfo.operatingSystem);
    info.Add("CPU", SystemInfo.processorType);
    info.Add("MEMORY", $"{SystemInfo.systemMemorySize} MB");
    info.Add("GPU", SystemInfo.graphicsDeviceName);
    info.Add("GPU MEMORY", $"{SystemInfo.graphicsMemorySize} MB");

    var sections = info.Select(section => $"{section.Key}: {section.Value}");
    return string.Join(Environment.NewLine, sections);
}
```

#### Try/Catch Reporting

Exceptions can be sent to BugSplat in a try/catch block by calling `Post`.

```csharp
try
{
    throw new Exception("BugSplat rocks!");
}
catch (Exception ex)
{
    StartCoroutine(bugsplat.Post(ex));
}
```

The default values specified on the instance of `BugSplat` can be overridden in the call to `Post`. Additionally, you can provide a `callback` to `Post` that will be invoked with the result once the upload is complete.

```csharp
var options = new ReportPostOptions()
{
    Description = "a new description",
    Email = "barney@bugsplat.com",
    Key = "a new key!",
    Notes = "some new notes!",
    User = "Barney"
};

options.AdditionalAttachments.Add(new FileInfo("/path/to/additional.txt"));

static void callback()
{
    Debug.Log($"Exception post callback!");
};

StartCoroutine(bugsplat.Post(ex, options, callback));
```

#### Preventing Repeated Reports

By default, BugSplat prevents reports from being sent at a rate greater than 1 per every 3 seconds. You can override the default crash report throttling implementation by setting `ShouldPostException` on your BugSplat instance. To override `ShouldPostException`, assign the property a new `Func<Exception, bool>` value. Be sure your new implementation can handle a null value for `Exception`!

The following example demonstrates how you could implement your own time-based report throttling mechanism:

```csharp
var lastPost = new DateTime(0);

bugsplat.ShouldPostException = (ex) =>
{
    var now = DateTime.Now;

    if (now - lastPost < TimeSpan.FromSeconds(3))
    {
        Debug.LogWarning("ShouldPostException returns false. Skipping BugSplat report...");
        return false;
    }

    Debug.Log("ShouldPostException returns true. Posting BugSplat report...");
    lastPost = now;

    return true;
};
```

#### Background Thread Exceptions

Unity raises `Application.logMessageReceived` only for logs written on the main thread. An unhandled exception on a background thread is written to the player log but never reaches that callback, so BugSplat captures these through `Application.logMessageReceivedThreaded` instead, buffers them, and posts them from the main thread on the next frame.

This is on by default and requires **Register Log Message Received**. Uncheck **Capture Exceptions On Background Threads** on your `BugSplatManager` to restore the previous behavior of reporting only main-thread exceptions.

Because the threaded callback also fires for main-thread logs that `logMessageReceived` already delivered, BugSplat ignores anything raised on the main thread there — main-thread exceptions are reported exactly once either way.

At most 64 background exceptions are buffered at a time. A thread failing in a tight loop can produce them faster than they can be uploaded, so the excess is dropped and a single warning is logged rather than queueing unbounded work.

#### Unobserved Task Exceptions

A `Task` that faults with nobody awaiting it never writes to the Unity log at all, so neither log callback sees it. BugSplat subscribes to `TaskScheduler.UnobservedTaskException` and posts these through the same main-thread queue as background thread exceptions. Each exception inside the `AggregateException` is reported separately, so unrelated failures land in separate buckets rather than one.

This is on by default and requires **Register Log Message Received**. Uncheck **Capture Unobserved Task Exceptions** on your `BugSplatManager` to disable it.

Two things are worth knowing about the timing. The runtime raises this event only when a garbage collection notices the faulted `Task`, so reports arrive well after the failure, and a `Task` that is never collected is never reported. And BugSplat deliberately does **not** call `SetObserved()` on these — marking the exception observed would suppress whatever your project does with it next, and reporting a failure must not change whether that failure happens.

#### Attaching Files to Native Crash Reports

`bugsplat.Attachments` and `ReportPostOptions.AdditionalAttachments` add files to the reports BugSplat posts from managed code. A native crash is captured and uploaded by the platform's own crash reporter, which never sees those lists, so files destined for native reports are attached separately with `AttachNativeLogFile`.

```csharp
bugsplat.AttachNativeLogFile("/path/to/support.log");
bugsplat.DetachNativeLogFile("/path/to/support.log");
```

Attaching is **additive and idempotent**. Every attached file is included in a native report, attaching one file never displaces another, and attaching the same file twice attaches it once. Paths are resolved to full paths before they are compared — case-insensitively on Windows — so `logs/support.log` and `C:\Game\Logs\Support.log` are recognized as the file they name rather than added as new attachments. `DetachNativeLogFile` removes a single file and leaves the rest attached. Both methods are safe to call from any thread, and both are ignored when native crash reporting isn't enabled for the platform, including in the editor.

| Platform | Native attachments                  |
| -------- | ----------------------------------- |
| Windows  | Multiple                            |
| macOS    | Multiple                            |
| iOS      | Multiple                            |
| Android  | Not supported — the call is a no-op |

`Player.log` still ships with managed posts on every platform, including Android.

`CapturePlayerLog` attaches `Application.consoleLogPath` through this same mechanism, so the two cooperate: turning it off detaches only `Player.log`, and attaching your own file leaves `Player.log` alone. Prefer `CapturePlayerLog` for that file rather than attaching `Application.consoleLogPath` yourself — see [Player Log and Privacy](#player-log-and-privacy).

{% hint style="info" %}
Native attachments on iOS are new in 5.0.0 — the iOS bridge was previously a no-op. Support comes from bugsplat-apple 3.5.0, which `com.bugsplat.unity` vendors, so there is nothing extra to install.
{% endhint %}

#### Player Log and Privacy

`CapturePlayerLog` is **enabled by default** on both construction paths — a new `BugSplatOptions` asset and a `BugSplat` created in code both start with it on — because `Player.log` is the most useful attachment on a crash report. WebGL is the exception: the platform has no `Player.log`, so a `BugSplat` created in code there defaults to off and the setting has no effect.

Be aware that Unity writes `Player.log` under the user's profile directory on every desktop platform, and that it records file system paths containing the operating system username. If you would rather not collect that, uncheck **Capture Player Log** on your options asset, or set the property in code:

```csharp
bugsplat.CapturePlayerLog = false;
```

The setting governs two surfaces, not one: whether `Player.log` is uploaded with managed posts, and whether it is attached to native crash reports on the platforms whose native reporter supports attachments. Assigning the property at runtime attaches or detaches the file immediately.

{% hint style="info" %}
`BugSplatOptions` assets created before 5.0.0 keep whatever value is already serialized in the asset file; only newly created assets pick up the default. Check the field on your existing asset if you want the new behavior.
{% endhint %}

#### Windows Minidumps (Crashes)

As of 5.0.0, Windows crashes are captured natively rather than by reading minidumps written by the `UnityCrashHandler`. See [🪟 Windows](#windows) for setup.

`PostCrash`, `PostMostRecentCrash`, and `PostAllCrashes` have been removed — unsent native crash reports upload automatically at startup, so no launch-time call is needed. `Post(FileInfo minidump)` still works on all platforms if you have your own minidump files to submit.

#### Windows Symbols

To enable the uploading of plugin symbols, generate an OAuth2 Client ID and Client Secret on the BugSplat [Integrations](https://app.bugsplat.com/v2/settings/database/integrations) page and provide them as described in [🔑 Symbol Upload](#symbol-upload). If your game contains Native Windows C++ plugins, `.dll` and `.pdb` files in the `Assets/Plugins/x86` and `Assets/Plugins/x86_64` folders will be uploaded by BugSplat's PostBuild script and used in symbolication.

For IL2CPP builds, BugSplat will also upload `LineNumberMappings.json`. Line mappings allow BugSplat to replace generated C++ function names, file names, and line numbers with their original C# equivalents.

#### Support Response

BugSplat has the ability to display a support response to users who encounter a crash. You can show your users a generalized support response for all crashes, or a custom support response that corresponds to the type of crash that occurred. Defining a support response allows you to alert users that bug has been fixed in a new version, or that they need to update their graphics drivers.

Next, pass a callback to `bugsplat.Post`. In the callback handler add code to open the support response in the user's browser. A full example can be seen in [ErrorGenerator.cs](https://github.com/BugSplat-Git/bugsplat-unity/blob/main/Samples~/my-unity-crasher/Scripts/ErrorGenerator.cs).

```csharp
private string infoUrl = "";

public void Event_CatchExceptionThenPostNewBugSplat()
{
    try
    {
        GenerateSampleStackFramesAndThrow();
    }
    catch (Exception ex)
    {
        var options = new ReportPostOptions()
        {
            Description = "a new description"
        };

        StartCoroutine(bugsplat.Post(ex, options, ExceptionCallback));
    }
}

void ExceptionCallback(ExceptionReporterPostResult result)
{
    UnityEngine.Debug.Log($"Exception post callback result: {result.Message}");

    if (result.Response == null) {
        return;
    }

    UnityEngine.Debug.Log($"BugSplat Status: {result.Response.status}");
    UnityEngine.Debug.Log($"BugSplat Crash ID: {result.Response.crashId}");
    UnityEngine.Debug.Log($"BugSplat Support URL: {result.Response.infoUrl}");

    infoUrl = result.Response.infoUrl;
}

private void OpenUrl(string url)
{
    var escaped = url.Replace("?", "\\?").Replace("&", "\\&").Replace(" ", "%20").Replace("!", "\\!");

#if UNITY_STANDALONE_WIN || UNITY_EDITOR_WIN || UNITY_WSA
    Process.Start(url);
#elif UNITY_STANDALONE_OSX || UNITY_EDITOR_OSX
    Process.Start("open", escaped);
#elif UNITY_STANDALONE_LINUX || UNITY_EDITOR_LINUX
    Process.Start("xdg-open", escaped);
#else
    UnityEngine.Debug.Log($"OpenUrl unsupported platform: {Application.platform}");
#endif
}
```

When an exception occurs, a page similar to the following will open in the user's browser on Windows, macOS, and Linux.

<figure><img src="../../../../.gitbook/assets/image (69).png" alt=""><figcaption></figcaption></figure>

More information on support responses can be found [here](https://docs.bugsplat.com/introduction/production/setting-up-custom-support-responses).

### 🤖 Android

The bugsplat-unity plugin supports crash reporting for native C++ crashes on Android via Crashpad. To configure crash reporting for Android, set the `UseNativeCrashReportingForAndroid` and `UploadDebugSymbolsForAndroid` properties to `true` on the BugSplatManager instance.

You'll also need to configure the scripting backend to use IL2CPP, target **ARM64**, and set the Minimum API Level to **Android 8.0 (API level 26)** or higher. ARM64 is the only configuration BugSplat tests; the bundled `bugsplat-android-release.aar` also ships `armeabi-v7a` and `x86_64` native libraries, but those ABIs are untested and unsupported.

<figure><img src="../../../../.gitbook/assets/image (70).png" alt=""><figcaption></figcaption></figure>

When you build your app for Android, be sure to set `Create symbols.zip` to `Debugging`

<figure><img src="../../../../.gitbook/assets/image (71).png" alt=""><figcaption></figcaption></figure>

#### ANR Detection

When `UseNativeCrashReportingForAndroid` is enabled, the bugsplat-unity plugin also reports ANRs (Application Not Responding events). No additional configuration is required.

On the next app launch, the SDK queries the OS for ANRs that occurred during the previous session, reads the system-provided thread dump, and uploads it to BugSplat. ANR reports appear alongside crashes with the **`Android.ANR`** type. The sample's **Crash Scenarios** menu has a `HANG` row that triggers an ANR for testing.

{% hint style="info" %}
ANR detection requires Android 11 (API level 30) or higher at runtime. On older versions it is silently disabled, while native crash reporting continues to work.
{% endhint %}

### 🍎 iOS

The bugsplat-unity plugin supports native crash reporting on iOS via bugsplat-apple, which uses PLCrashReporter to capture crashes via Mach exception handling. To configure crash reporting for iOS, set the `UseNativeCrashReportingForIos` and `UploadDebugSymbolsForIos` properties to `true` on the BugSplatManager instance.

When native crash reporting is enabled, BugSplat disables Unity's built-in crash reporter during the Xcode export to prevent it from conflicting with PLCrashReporter. Crashes are captured at crash time and uploaded on the next app launch.

For IL2CPP builds, BugSplat will also upload `LineNumberMappings.json` alongside dSYMs, so IL2CPP-generated C++ symbols map back to their original C# method names, file names, and line numbers.

#### Hang Detection

When `UseNativeCrashReportingForIos` is enabled, the bugsplat-unity plugin also detects fatal main-thread hangs. No additional configuration is required.

If the main thread stays unresponsive past the detection threshold and the app is then terminated without recovering (by the OS watchdog at launch or resume, or by the user force-quitting), BugSplat uploads a hang report on the next launch. Hangs the app recovers from are not reported. Hang reports carry the exception name **`App Hang (Fatal)`**. The sample's **Crash Scenarios** menu has a `HANG` row that triggers a hang for testing.

{% hint style="info" %}
Hang detection is suppressed while a debugger is attached. Test it on a build run without the Xcode debugger.
{% endhint %}

### 🖥 macOS

The bugsplat-unity plugin supports native crash reporting on macOS via bugsplat-apple, which uses PLCrashReporter to capture crashes via Mach exception handling. Native macOS crash reporting requires the `IL2CPP` scripting backend.

To configure crash reporting for macOS, set the `UseNativeCrashReportingForMac` and `UploadDebugSymbolsForMac` properties to `true` on your `BugSplatOptions` asset. For IL2CPP builds, BugSplat will upload dSYMs and `LineNumberMappings.json` for full symbolication.

`Player.log` is attached to native macOS crash reports when `CapturePlayerLog` is enabled on your `BugSplatOptions` asset. See [Attaching Files to Native Crash Reports](#attaching-files-to-native-crash-reports) to add files of your own.

### 🪟 Windows

The bugsplat-unity plugin supports native crash reporting on Windows via [BugSplat for Windows](../desktop/cplusplus/). Unlike the other platforms, Windows native crash reporting works with **both** the `Mono` and `IL2CPP` scripting backends, on x86 (32-bit), x64, and Windows-on-ARM (ARM64).

To configure crash reporting for Windows, set the `UseNativeCrashReportingForWindows` property to `true` on your `BugSplatOptions` asset.

When native crash reporting is enabled:

* Native crashes are captured at crash time and uploaded immediately. Reports that can't be uploaded — for example, when the user is offline — are uploaded automatically on the next launch.
* `Player.log` is attached to native crash reports when `CapturePlayerLog` is enabled on your `BugSplatOptions` asset, and assigning the property at runtime adds or removes the attachment. See [Attaching Files to Native Crash Reports](#attaching-files-to-native-crash-reports) to add files of your own.
* The BugSplat crash dialog is shown by default. Set `WindowsShowCrashDialog` to `false` to send reports silently.
* At build time, BugSplat copies `BugSplatMonitor.exe`, `BugSplatRc.dll`, and `BugSplatWer.dll` next to your game's executable. **These files are required for crash reporting and must ship alongside your executable in your installer.**

For IL2CPP builds, BugSplat copies `LineNumberMappings.json` into the build and uploads it with your symbols, so IL2CPP-generated C++ frames symbolicate back to C# method names, file names, and line numbers.

{% hint style="info" %}
The native library is a standard `/MD` binary and depends on the Microsoft Visual C++ Redistributable (`vcruntime140.dll`, `msvcp140.dll`), which Unity Windows players already require. If it is missing, native crash reporting fails to initialize with an error in the log and .NET exception reporting continues to work.
{% endhint %}

#### Windows Error Reporting

Some crashes terminate a process without giving any in-process code a chance to run — most commonly stack buffer overruns (`0xC0000409`, which is also what `__fastfail` produces) and heap corruption (`0xC0000374`). BugSplat's crash handler never sees these, so Windows Error Reporting hands them over instead. That is what `BugSplatWer.dll` is for.

Two things must be true for it to work:

1. `BugSplatWer.dll` sits next to your game's executable. The post-build step already does this.
2. A machine-wide registry value names that DLL's full path. Under `HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\RuntimeExceptionHelperModules`, add a `REG_DWORD` whose **name** is the absolute path to the installed `BugSplatWer.dll`. The data is ignored. This lives in `HKLM`, so it requires administrator rights.

**Your installer is responsible for the second step**, and for removing the value on uninstall:

```batch
reg add "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\RuntimeExceptionHelperModules" /v "C:\Program Files\MyGame\BugSplatWer.dll" /t REG_DWORD /d 0 /f
reg delete "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\RuntimeExceptionHelperModules" /v "C:\Program Files\MyGame\BugSplatWer.dll" /f
```

The value name must match the installed path exactly, using backslashes. Moving or reinstalling the game to a different folder silently disarms it.

For local builds, use **BugSplat > Windows > Register WER Handler** in the Unity editor. It asks for your built player, writes the value elevated, and reads it back to confirm. **Unregister WER Handler** removes it again, and **Check WER Handler Registration** reports the current state.

At runtime, `bugsplat.WindowsWerEnabled` reports whether the handler registered. When it hasn't, BugSplat logs what will be missed and how to fix it — a warning in development builds, informational otherwise. All other crashes are reported normally either way.

When a report doesn't arrive, two places are worth checking: `%TEMP%\BugSplat\<Application>-<Version>\<GUID>\` holds the SDK's own logs, including `BugSplatWer.log`, and `%LOCALAPPDATA%\CrashDumps` collects dumps Windows wrote because nothing claimed the crash.

{% hint style="info" %}
If registration appears to succeed but `WindowsWerEnabled` stays `false`, check your endpoint-protection software — `RuntimeExceptionHelperModules` is a known persistence location and some products monitor or block writes to it.
{% endhint %}

{% hint style="info" %}
BugSplat installs its handler with `SetUnhandledExceptionFilter` and then prevents that filter from being replaced, so other middleware in your project cannot install a top-level exception filter after BugSplat initializes.
{% endhint %}

#### Windows Hang Detection

Set `WindowsHangDetectionTimeoutMs` to a non-zero value to report hangs when your game's main thread stops responding for longer than the configured timeout. When a hang is detected, BugSplat captures a hang report, uploads it, and **terminates the hung process**.

Hang detection is **disabled by default** (`0`) because long frames — loading screens, synchronous asset operations — can be falsely reported as hangs, and a false positive terminates your game. If you enable it, choose a timeout comfortably longer than your longest expected frame.

### 🔑 Symbol Upload

BugSplat uploads symbols for you as a post-build step. It authenticates with an OAuth2 Client ID and Client Secret generated on the BugSplat [Integrations](https://app.bugsplat.com/v2/settings/database/integrations) page, and those credentials are **specific to one database**.

Credentials are never stored in your project — an asset carrying them ends up in version control and inside shipped builds. They resolve in this order:

1. **The `SYMBOL_UPLOAD_CLIENT_ID` and `SYMBOL_UPLOAD_CLIENT_SECRET` environment variables.** Use these in CI. They are the names the `symbol-upload` CLI reads, so the same pair works whether Unity runs the upload or your CI runs `xcodebuild` itself.
2. **`~/.bugsplat/credentials/<database>.sh`.** For local development. Write it from **BugSplat > Symbol Upload > Set Credentials**, which creates one file per database, so a single machine can hold credentials for as many databases as you work with.

**Clear Credentials** deletes the current project's file, and **Check Credentials** reports which source a build would use. If neither source supplies both values, symbol upload is skipped with a warning and the build still succeeds — on iOS as an Xcode build warning, since that upload runs during the Xcode build rather than the Unity one.

Because the file lives in your home directory rather than the project, there is nothing to add to `.gitignore` and nothing to strip out of a build.

| Variable                    | Description                                                                                                                                                                                                                                                                    |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| SYMBOL_UPLOAD_CLIENT_ID     | An OAuth2 Client ID used for uploading [symbol files](https://docs.bugsplat.com/introduction/development/working-with-symbol-files). Takes precedence over `~/.bugsplat/credentials/<database>.sh`                                                                              |
| SYMBOL_UPLOAD_CLIENT_SECRET | An OAuth2 Client Secret used for uploading [symbol files](https://docs.bugsplat.com/introduction/development/working-with-symbol-files). Takes precedence over `~/.bugsplat/credentials/<database>.sh`                                                                          |

{% hint style="warning" %}
`SymbolUploadClientId` and `SymbolUploadClientSecret` were removed from `BugSplatOptions` in 5.0.0, and the environment variables were renamed from `BUGSPLAT_CLIENT_ID` and `BUGSPLAT_CLIENT_SECRET`. The old names are no longer read. **If an options asset holding credentials has ever been committed, rotate them** — prior versions serialized both values into player builds and into the generated `project.pbxproj`.
{% endhint %}

### 🧩 API

The following API methods are available to help you customize BugSplat to fit your needs.

#### BugSplatManager

| Setting                              | Description                                                                                                                                                                           |
| ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| DontDestroyManagerOnSceneLoad        | Should the BugSplat Manager persist through scene loads?                                                                                                                              |
| RegisterLogMessageReceived           | Register a callback function and allow BugSplat to capture instances of LogType.Exception.                                                                                            |
| CaptureExceptionsOnBackgroundThreads | Also capture unhandled exceptions thrown on background threads (default). Requires RegisterLogMessageReceived. See [Background Thread Exceptions](#background-thread-exceptions).     |
| CaptureUnobservedTaskExceptions      | Also capture exceptions from Tasks that faulted and were never awaited (default). Requires RegisterLogMessageReceived. See [Unobserved Task Exceptions](#unobserved-task-exceptions). |

#### BugSplat Options

| Option                            | Description                                                                                                                                                                                                                                            |
| --------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Database                          | The name of your BugSplat database.                                                                                                                                                                                                                    |
| Application                       | The name of your BugSplat application. Defaults to Application.productName if no value is set.                                                                                                                                                         |
| Version                           | The version of your BugSplat application. Defaults to Application.version if no value is set.                                                                                                                                                          |
| Description                       | A default description that can be overridden by call to Post.                                                                                                                                                                                          |
| Email                             | A default email that can be overridden by call to Post.                                                                                                                                                                                                |
| Key                               | A default key that can be overridden by call to Post.                                                                                                                                                                                                  |
| Notes                             | A default general purpose field that can be overridden by call to post                                                                                                                                                                                 |
| User                              | A default user that can be overridden by call to Post                                                                                                                                                                                                  |
| CaptureEditorLog                  | Should BugSplat upload Editor.log when Post is called                                                                                                                                                                                                  |
| CapturePlayerLog                  | Should BugSplat upload Player.log when Post is called, and attach it to native crash reports on platforms whose native reporter supports attachments. Enabled by default — see [Player Log and Privacy](#player-log-and-privacy)                       |
| LogFileMaxSizeMB                  | Maximum size in MB of the log files uploaded with a report. Defaults to 10                                                                                                                                                                             |
| CaptureScreenshots                | Should BugSplat a screenshot and upload it when Post is called                                                                                                                                                                                         |
| PostExceptionsInEditor            | Should BugSplat upload exceptions when in editor                                                                                                                                                                                                       |
| PersistentDataFileAttachmentPaths | Paths to files (relative to Application.persistentDataPath) to upload with each report                                                                                                                                                                 |
| Attributes                        | Name/value pairs attached to every report, authored on the options asset                                                                                                                                                                               |
| UseNativeCrashReportingForIos     | Should BugSplat enable native crash reporting on iOS                                                                                                                                                                                                   |
| UploadDebugSymbolsForIos          | Should BugSplat add an Xcode build phase that uploads dSYMs and LineNumberMappings.json for iOS builds                                                                                                                                                 |
| UseNativeCrashReportingForAndroid | Should BugSplat enable native crash reporting on Android. Requires IL2CPP and ARM64                                                                                                                                                                    |
| UploadDebugSymbolsForAndroid      | Should BugSplat upload the generated symbols archive for Android builds                                                                                                                                                                                |
| UseNativeCrashReportingForMac     | Should BugSplat enable native crash reporting on macOS                                                                                                                                                                                                  |
| UploadDebugSymbolsForMac          | Should BugSplat upload dSYMs and LineNumberMappings.json for macOS builds                                                                                                                                                                                |
| UseNativeCrashReportingForWindows | Should BugSplat enable native crash reporting on Windows. Works with both Mono and IL2CPP                                                                                                                                                               |
| WindowsShowCrashDialog            | Show the BugSplat crash dialog when a native crash occurs on Windows (default). When disabled, reports are sent silently                                                                                                                                |
| WindowsHangDetectionTimeoutMs     | Native hang detection timeout in milliseconds for Windows. 0 (default) disables hang detection                                                                                                                                                          |

{% hint style="info" %}
`ShouldPostException` is not a field on the `BugSplatOptions` asset. It is a runtime-only property you assign on your `BugSplat` instance in code — see [Preventing Repeated Reports](#preventing-repeated-reports).
{% endhint %}

### 💬 User Feedback

In addition to crash reporting, BugSplat supports collecting non-crashing user feedback such as bug reports and feature requests. Feedback reports appear in BugSplat with the "User Feedback" type, grouped by title.

```csharp
var bugsplat = FindFirstObjectByType<BugSplatManager>().BugSplat;
StartCoroutine(bugsplat.PostFeedback("Login button broken", "Nothing happens when I tap it"));
```

You can customize feedback submissions with `ReportPostOptions`:

```csharp
var options = new ReportPostOptions()
{
    Email = "jane@example.com",
    User = "Jane",
    Description = "The sidebar overlaps the main content."
};
options.AdditionalAttachments.Add(new FileInfo("/path/to/screenshot.png"));

StartCoroutine(bugsplat.PostFeedback("UI rendering issue", "The sidebar overlaps.", options));
```

{% hint style="info" %}
`PostFeedback` is unavailable on WebGL, which has no feedback client. The call logs an error and invokes your callback with `null` rather than uploading.
{% endhint %}

### 🚚 Migrating from 4.x

Version 5.0.0 replaces the Unity crash-folder minidump flow with native crash reporting and tightens up a few defaults. What you need to change:

* **`PostAllCrashes`, `PostCrash`, and `PostMostRecentCrash` have been removed.** Unsent native crash reports upload automatically at startup, so there is nothing to call at launch. Delete any calls to these methods.
* **Unity's `CrashReporting.crashReportFolder` minidumps are no longer read or uploaded.** `Post(FileInfo minidump)` still works for posting your own minidump files, and now does so on every platform except WebGL — it was previously implemented for Windows and WSA only.
* **`SymbolUploadClientId` and `SymbolUploadClientSecret` have been removed from `BugSplatOptions`.** Storing them there put the secret in version control and inside shipped builds. Set them per database from **BugSplat > Symbol Upload > Set Credentials**, or with environment variables in CI. Those variables are renamed from `BUGSPLAT_CLIENT_ID` and `BUGSPLAT_CLIENT_SECRET` to `SYMBOL_UPLOAD_CLIENT_ID` and `SYMBOL_UPLOAD_CLIENT_SECRET`, and the old names are no longer read. See [🔑 Symbol Upload](#symbol-upload).
* **iOS projects exported with Append, or checked into version control, keep their old "Upload dSYM files to BugSplat" build phase.** Unity matches an existing phase on its script body, so the rewritten phase isn't recognized as the same one. Delete the old phase and build again, or re-export with Replace. **A phase generated before 5.0.0 contains your Client ID and Secret in plain text — rotate them.**
* **`BugSplatOptions.Attributes` is now a list of name/value pairs** rather than a `Dictionary<string, string>`, which Unity could never serialize. Unity drops the old serialized value silently when a 4.x options asset is opened, so re-enter any attributes you had authored in code.
* **A few types moved out of the global namespace**, where they were being injected into every consumer project. `BuildPostprocessors`, `BugSplatOptionsEditor`, and `BugSplatSymbolUploadCredentials` are now in `BugSplatUnity.Editor`, and `BugSplatRef` is in `BugSplatUnity.Runtime.Manager` and is `internal`. Scenes and prefabs reference scripts by file GUID, so no asset needs re-linking, but code naming these types needs a `using`. Use `BugSplatManager.BugSplat` rather than `BugSplatRef`. `BugSplat`, `BugSplatOptions`, and `BugSplatManager` are unaffected.
* **Coroutines that `yield return bugsplat.Post(...)` now genuinely wait for the upload.** They previously resumed after a single frame, so `yield return bugsplat.Post(ex); Application.Quit();` lost reports nondeterministically.
* **Post callbacks now run on the main thread.** They previously ran on a threadpool thread, so any callback touching a Unity API threw.

### 🧑‍💻 Contributing

BugSplat ❤️s open source! If you feel that this package can be improved, please open an [Issue](https://github.com/BugSplat-Git/bugsplat-unity/issues). If you have an awesome new feature you'd like to implement, we'd love to merge your [Pull Request](https://github.com/BugSplat-Git/bugsplat-unity/pulls). You can also send us an [email](mailto:support@bugsplat.com), join us on [Discord](https://discord.gg/K4KjjRV5ve), or message us via the in-app chat on [bugsplat.com](https://bugsplat.com/).
