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
BugSplat recommends building with the IL2CPP backend for the best crash reporting experience. For more information, please see the [Player Settings](https://github.com/BugSPlat-Git/bugsplat-unity#-player-settings) section.
{% endhint %}

After installing `com.bugsplat.unity`, you can import a sample project to help you get started with BugSplat. Click here if you'd like to skip the sample project and get straight to the [usage](https://github.com/BugSPlat-Git/bugsplat-unity#usage) instructions.

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

### ⌨️ Usage

If you're using `BugSplatOptions` and `BugSplatManager`, BugSplat automatically configures an `Application.logMessageReceived` handler that will post reports when it encounters a log message of type `Exception`. You can also extend your BugSplat integration and [customize report metadata](https://github.com/BugSPlat-Git/bugsplat-unity#adding-metadata), [report exceptions in try/catch blocks](https://github.com/BugSPlat-Git/bugsplat-unity#trycatch-reporting), [prevent repeated reports](https://github.com/BugSPlat-Git/bugsplat-unity#preventing-repeated-reports), and [capture native crashes on Windows](https://github.com/BugSPlat-Git/bugsplat-unity#-windows).

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

#### Windows Minidumps (Crashes)

As of 5.0.0, Windows crashes are captured natively rather than by reading minidumps written by the `UnityCrashHandler`. See [🪟 Windows](#windows) for setup.

`PostCrash`, `PostMostRecentCrash`, and `PostAllCrashes` have been removed — unsent native crash reports upload automatically at startup, so no launch-time call is needed. `Post(FileInfo minidump)` still works on all platforms if you have your own minidump files to submit.

#### Windows Symbols

To enable the uploading of plugin symbols, generate an OAuth2 Client ID and Client Secret on the BugSplat [Integrations](https://app.bugsplat.com/v2/settings/database/integrations) page. Add your Client ID and Client Secret to the `BugSplatOptions` object you generated in the [Configuration](https://github.com/BugSPlat-Git/bugsplat-unity#%E2%9A%99%EF%B8%8F-configuration) section. Alternatively, you can set the `BUGSPLAT_CLIENT_ID` and `BUGSPLAT_CLIENT_SECRET` environment variables; if set, these are used instead of `SymbolUploadClientId`/`SymbolUploadClientSecret`. If your game contains Native Windows C++ plugins, `.dll` and `.pdb` files in the `Assets/Plugins/x86` and `Assets/Plugins/x86_64` folders will be uploaded by BugSplat's PostBuild script and used in symbolication.

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

You'll also need to configure the scripting backend to use IL2CPP, target ARM64 (ARMV7a is not supported), and set the Minimum API Level to Android 8.0 (API level 26) or higher.

<figure><img src="../../../../.gitbook/assets/image (70).png" alt=""><figcaption></figcaption></figure>

When you build your app for Android, be sure to set `Create symbols.zip` to `Debugging`

<figure><img src="../../../../.gitbook/assets/image (71).png" alt=""><figcaption></figcaption></figure>

#### ANR Detection

When native crash reporting is enabled, the bugsplat-unity plugin also reports ANRs (Application Not Responding events). No additional configuration is required.

On the next app launch, the SDK queries the OS for ANRs that occurred during the previous session, reads the system-provided thread dump, and uploads it to BugSplat. ANR reports appear alongside crashes with the **`Android.ANR`** type. The sample project's **Hang / ANR** button triggers an ANR for testing.

{% hint style="info" %}
ANR detection requires Android 11 (API level 30) or higher at runtime. On older versions it is silently disabled, while native crash reporting continues to work.
{% endhint %}

### 🍎 iOS

The bugsplat-unity plugin supports crash reporting for native C++ crashes on iOS via bugsplat-apple. To configure crash reporting for iOS, set the `UseNativeCrashReportingForIos` and `UploadDebugSymbolsForIos` properties to `true` on the BugSplatManager instance.

#### Hang Detection

When native crash reporting is enabled, the bugsplat-unity plugin also detects fatal main-thread hangs. No additional configuration is required.

If the main thread stays unresponsive past the detection threshold and the app is then terminated without recovering (by the OS watchdog at launch or resume, or by the user force-quitting), BugSplat uploads a hang report on the next launch. Hangs the app recovers from are not reported. Hang reports carry the exception name **`App Hang (Fatal)`**. The sample project's **Hang / ANR** button triggers a hang for testing.

{% hint style="info" %}
Hang detection is suppressed while a debugger is attached. Test it on a build run without the Xcode debugger.
{% endhint %}

### 🖥 macOS

The bugsplat-unity plugin supports native crash reporting on macOS via bugsplat-apple, which uses PLCrashReporter to capture crashes via Mach exception handling. Native macOS crash reporting requires the `IL2CPP` scripting backend.

To configure crash reporting for macOS, set the `UseNativeCrashReportingForMac` and `UploadDebugSymbolsForMac` properties to `true` on your `BugSplatOptions` asset. For IL2CPP builds, BugSplat will upload dSYMs and `LineNumberMappings.json` for full symbolication.

### 🪟 Windows

The bugsplat-unity plugin supports native crash reporting on Windows via [BugSplat for Windows](../desktop/cplusplus/). Unlike the other platforms, Windows native crash reporting works with **both** the `Mono` and `IL2CPP` scripting backends, on x86 (32-bit), x64, and Windows-on-ARM (ARM64).

To configure crash reporting for Windows, set the `UseNativeCrashReportingForWindows` property to `true` on your `BugSplatOptions` asset.

When native crash reporting is enabled:

* Native crashes are captured at crash time and uploaded immediately. Reports that can't be uploaded — for example, when the user is offline — are uploaded automatically on the next launch.
* `Player.log` is attached to native crash reports automatically.
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

For local builds, use **BugSplat > Windows > Register WER Handler** in the Unity editor. It asks for your built player, writes the value elevated, and reads it back to confirm. **Check WER Handler Registration** reports the current state.

At runtime, `bugsplat.WindowsWerEnabled` reports whether the handler registered. When it hasn't, BugSplat logs what will be missed and how to fix it — a warning in development builds, informational otherwise. All other crashes are reported normally either way.

{% hint style="info" %}
If registration appears to succeed but `WindowsWerEnabled` stays `false`, check your endpoint-protection software — `RuntimeExceptionHelperModules` is a known persistence location and some products monitor or block writes to it.
{% endhint %}

#### Windows Hang Detection

Set `WindowsHangDetectionTimeoutMs` to a non-zero value to report hangs when your game's main thread stops responding for longer than the configured timeout. When a hang is detected, BugSplat captures a hang report, uploads it, and **terminates the hung process**.

Hang detection is **disabled by default** (`0`) because long frames — loading screens, synchronous asset operations — can be falsely reported as hangs, and a false positive terminates your game. If you enable it, choose a timeout comfortably longer than your longest expected frame.

### 🧩 API

The following API methods are available to help you customize BugSplat to fit your needs.

#### BugSplatManager

| Setting                       | Description                                                                                |
| ----------------------------- | ------------------------------------------------------------------------------------------ |
| DontDestroyManagerOnSceneLoad | Should the BugSplat Manager persist through scene loads?                                   |
| RegisterLogMessageReceived    | Register a callback function and allow BugSplat to capture instances of LogType.Exception. |

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
| CapturePlayerLog                  | Should BugSplat upload Player.log when Post is called                                                                                                                                                                                                  |
| CaptureScreenshots                | Should BugSplat a screenshot and upload it when Post is called                                                                                                                                                                                         |
| PostExceptionsInEditor            | Should BugSplat upload exceptions when in editor                                                                                                                                                                                                       |
| PersistentDataFileAttachmentPaths | Paths to files (relative to Application.persistentDataPath) to upload with each report                                                                                                                                                                 |
| ShouldPostException               | Settable guard function that is called before each BugSplat report is posted                                                                                                                                                                           |
| SymbolUploadClientId              | An OAuth2 Client ID value used for uploading [symbol files](https://docs.bugsplat.com/introduction/development/working-with-symbol-files) generated via BugSplat's [Integrations](https://app.bugsplat.com/v2/settings/database/integrations) page     |
| SymbolUploadClientSecret          | An OAuth2 Client Secret value used for uploading [symbol files](https://docs.bugsplat.com/introduction/development/working-with-symbol-files) generated via BugSplat's [Integrations](https://app.bugsplat.com/v2/settings/database/integrations) page |
| UseNativeCrashReportingForMac     | Should BugSplat enable native crash reporting on macOS                                                                                                                                                                                                  |
| UploadDebugSymbolsForMac          | Should BugSplat upload dSYMs and LineNumberMappings.json for macOS builds                                                                                                                                                                                |
| UseNativeCrashReportingForWindows | Should BugSplat enable native crash reporting on Windows. Works with both Mono and IL2CPP                                                                                                                                                               |
| WindowsShowCrashDialog            | Show the BugSplat crash dialog when a native crash occurs on Windows (default). When disabled, reports are sent silently                                                                                                                                |
| WindowsHangDetectionTimeoutMs     | Native hang detection timeout in milliseconds for Windows. 0 (default) disables hang detection                                                                                                                                                          |

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

### 🧑‍💻 Contributing

BugSplat ❤️s open source! If you feel that this package can be improved, please open an [Issue](https://github.com/BugSplat-Git/bugsplat-unity/issues). If you have an awesome new feature you'd like to implement, we'd love to merge your [Pull Request](https://github.com/BugSplat-Git/bugsplat-unity/pulls). You can also send us an [email](mailto:support@bugsplat.com), join us on [Discord](https://discord.gg/K4KjjRV5ve), or message us via the in-app chat on [bugsplat.com](https://bugsplat.com/).
