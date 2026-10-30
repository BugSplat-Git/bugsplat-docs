# Unity

BugSplat's `com.bugsplat.unity` package provides crash and exception reporting for Unity projects. It captures C# exceptions on every platform, native crashes and hangs on Windows, macOS, iOS, and Android, and attaches log files and screenshots so you can fix bugs and deliver a better user experience.

Before you proceed, please make sure you have completed the following checklist:

* [Sign Up](https://app.bugsplat.com/v2/sign-up) as a new BugSplat user
* [Log In](https://app.bugsplat.com/cognito/login) to your account
* Note the name of the database in your account that this project will report to — you'll enter it in Project Settings

{% hint style="info" %}
Upgrading from 4.x? Version 5.0.0 no longer needs anything placed in a scene and removes the minidump upload methods. See [Migrating from 4.x](unity/migrating-from-4x.md).
{% endhint %}

### 🏗 Installation

BugSplat's `com.bugsplat.unity` package can be added to your project via [OpenUPM](https://openupm.com/packages/com.bugsplat.unity/) or a URL to our git [repository](https://github.com/BugSplat-Git/bugsplat-unity.git).

#### OpenUPM

Information on installing OpenUPM can be found [here](https://openupm.com/). After installing OpenUPM, run the following command to add BugSplat to your project.

```sh
openupm add com.bugsplat.unity
```

#### Git

Information on adding a Unity package via a git URL can be found [here](https://docs.unity3d.com/Manual/upm-ui-giturl.html).

```
https://github.com/BugSplat-Git/bugsplat-unity.git
```

### 🧑‍🏫 Sample

{% hint style="success" %}
BugSplat recommends building with the IL2CPP backend for the best crash reporting experience. For more information, please see the Player Settings section below.
{% endhint %}

The package ships a sample, **my-unity-crasher**, to help you get started. If you'd rather skip it, go straight to the Configuration section below.

To import the sample, open **Window > Package Manager**, select **BugSplat**, and click the caret next to **Samples** to reveal **my-unity-crasher**. Click **Import** to add the sample to your project.

<figure><img src="../../../../.gitbook/assets/image (58).png" alt="Importing the my-unity-crasher sample from Package Manager"><figcaption></figcaption></figure>

In the Project Assets browser, open the **Sample** scene from `Samples > BugSplat > Version > my-unity-crasher > Scenes`.

Next, open **Edit > Project Settings > BugSplat**, click **Create** (or pick your project's existing options asset), and enter the name of a database from your BugSplat account. The sample ships no options asset of its own, so it always reports to your database. To exercise everything it offers, also check **Post Exceptions In Editor** and every platform under **Upload Debug Symbols For** — see the [sample's README](https://github.com/BugSplat-Git/bugsplat-unity/blob/main/Samples~/my-unity-crasher/README.md).

{% hint style="info" %}
The sample's UI labels use TextMeshPro. If the button text appears blank, import TMP Essentials via **Window > TextMeshPro > Import TMP Essential Resources**.
{% endhint %}

Click **Play** and run a scenario from the **Crash Scenarios** menu to send an error report to BugSplat. Scenarios are grouped by the mechanism that captures them, and the menu is platform-aware — each platform lists only what it can capture. Native scenarios are disabled in the editor and need a built player; see the [sample's README](https://github.com/BugSplat-Git/bugsplat-unity/blob/main/Samples~/my-unity-crasher/README.md) for the full scenario matrix.

To view the error report, navigate to the BugSplat [Dashboard](https://app.bugsplat.com/v2/dashboard) and ensure you have selected the correct database. Navigate to the [Crashes](https://app.bugsplat.com/v2/crashes) page, and click the value in the ID column to see the details of your report, including the call stack, log file, and screenshot of your app when the error occurred.

<figure><img src="../../../../.gitbook/assets/image (62).png" alt="A Unity crash report in BugSplat"><figcaption></figcaption></figure>

### 🧰 Player Settings

For best results, BugSplat recommends building with the `IL2CPP` backend. The `Mono` backend is supported, but has several limitations. With `IL2CPP`, BugSplat can capture fully symbolicated C# exception traces in production, as well as native crashes that contain call stacks mapped back to their original C# function names, file names, and line numbers.

To optimize your game for crash reporting, open **Edit > Project Settings > Player** and navigate to the **Configuration** section. For **Scripting Backend** choose `IL2CPP`, and for **IL2CPP Stacktrace Information** choose `Method Name, File Name, and Line Number`.

<figure><img src="../../../../.gitbook/assets/image (63).png" alt="Unity Player Settings with IL2CPP selected"><figcaption></figcaption></figure>

### ⚙️ Configuration

Open **Edit > Project Settings > BugSplat** (also available from **BugSplat > Settings...**) and click **Create**, or select an existing `BugSplatOptions` asset, then enter the name of a database from your BugSplat account ([sign up](https://app.bugsplat.com/v2/sign-up) if you don't have one). That is the whole setup: BugSplat initializes itself from that asset before the first scene loads, in the editor and in every player, and nothing needs to be placed in a scene.

<!-- TODO: screenshot of Edit > Project Settings > BugSplat -->

If **Application** or **Version** are left empty, BugSplat defaults them to `Application.productName` and `Application.version`. Every other option is described in the [Options Reference](unity/api.md).

Exceptions thrown in the editor are not uploaded by default, so play mode errors never reach the database you ship with. The first exception skipped in a play session logs a warning that names the setting. Check **Post Exceptions In Editor** on the options asset (or set `BugSplat.Instance.PostExceptionsInEditor = true` in code) while you verify your integration.

A **release** build fails if no options asset is selected or its database is empty, so a misconfigured project cannot ship a player that silently reports nothing. A **development** build only warns, so iterating never requires BugSplat to be filled in first.

Not using BugSplat yet, or only in QA and release? Uncheck **Enabled** on the asset, or define `BUGSPLAT_DISABLED` for the build targets you want it off for. Both mean no initialization and no build validation — see [Turning BugSplat On and Off](unity/api.md#turning-bugsplat-on-and-off).

Your code reaches the client through `BugSplat.Instance`, which is ready inside any `Awake`:

```csharp
BugSplat.Instance.Attributes["level"] = currentLevel;
```

To start BugSplat yourself — after a consent screen, for example — turn off **Initialize Automatically** on the asset and call `BugSplat.Initialize()` when you are ready. To change values at runtime, such as a database chosen per environment, pass them in: `BugSplat.Initialize(o => o.Database = database)` initializes from a copy of the asset with your changes applied.

Scripts, CI, and AI agents can do all of this without the Editor UI: one options asset on disk is enough, and there is a batch-mode command. See [Scripted Setup](unity/automation.md).

### ⌨️ Usage

BugSplat registers an `Application.logMessageReceived` handler that posts a report whenever it encounters a log message of type `Exception`. You can also extend your integration by adding metadata, reporting exceptions from try/catch blocks, throttling reports, and collecting user feedback.

#### Adding Metadata

Your instance of `BugSplat` is `BugSplat.Instance`. It is set before the first scene loads, so it is safe to read in any `Awake`:

```csharp
var bugsplat = BugSplat.Instance;
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

`Attachments` reaches managed reports only. To attach files to native crash reports, see [Attaching Files to Native Crash Reports](unity/api.md#attaching-files-to-native-crash-reports).

You can use the `Notes` field to capture arbitrary data such as system information:

```csharp
void Start()
{
    bugsplat = BugSplat.Instance;
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

void callback(ExceptionReporterPostResult result)
{
    Debug.Log($"Exception post callback: {result.Message}");
}

StartCoroutine(bugsplat.Post(ex, options, callback));
```

#### Preventing Repeated Reports

By default, BugSplat prevents reports from being sent at a rate greater than 1 per every 3 seconds. You can override the default throttling by assigning a new `Func<Exception, bool>` to `ShouldPostException` on your BugSplat instance. Be sure your implementation can handle a null value for `Exception`!

The following example demonstrates how you could implement your own time-based throttling:

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

Unity raises `Application.logMessageReceived` only for logs written on the main thread, so BugSplat also listens to `Application.logMessageReceivedThreaded`, buffers unhandled exceptions from background threads, and posts them from the main thread on the next frame. Main-thread exceptions are still reported exactly once.

This is on by default. Uncheck **Capture Exceptions On Background Threads** on your options asset to report main-thread exceptions only. At most 64 background exceptions are buffered at a time; the excess is dropped with a single warning.

#### Unobserved Task Exceptions

A `Task` that faults with nobody awaiting it never writes to the Unity log at all. BugSplat subscribes to `TaskScheduler.UnobservedTaskException` and posts these through the same main-thread queue, reporting each exception inside the `AggregateException` separately.

This is on by default. Uncheck **Capture Unobserved Task Exceptions** on your options asset to disable it. The runtime raises this event only when a garbage collection notices the faulted `Task`, so reports arrive well after the failure, and a `Task` that is never collected is never reported. BugSplat does **not** call `SetObserved()` on these, so reporting never changes what your project does with the exception.

#### User Feedback

In addition to crash reporting, BugSplat supports collecting non-crashing [user feedback](../../../development/web-services/user-feedback.md) such as bug reports and feature requests. Feedback reports appear in BugSplat with the "User Feedback" type, grouped by title.

```csharp
StartCoroutine(BugSplat.Instance.PostFeedback("Login button broken", "Nothing happens when I tap it"));
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

StartCoroutine(BugSplat.Instance.PostFeedback("UI rendering issue", "The sidebar overlaps.", options));
```

User feedback is supported on every platform except WebGL, where it logs an error instead.

#### Support Responses

BugSplat can display a [support response](../../../production/setting-up-custom-support-responses.md) to users who encounter a crash. You can show a generalized response for all crashes, or a custom response that corresponds to the type of crash that occurred — for example, to tell users a bug has been fixed in a new version, or that they need to update their graphics drivers.

Pass a callback to `bugsplat.Post`, read `infoUrl` from the response, and open it in the user's browser:

```csharp
private string infoUrl = "";

public void CatchExceptionThenPostNewBugSplat()
{
    try
    {
        MethodThatThrows();
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

#if UNITY_STANDALONE_WIN || UNITY_EDITOR_WIN
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

<figure><img src="../../../../.gitbook/assets/image (69).png" alt="A BugSplat support response page"><figcaption></figcaption></figure>

#### Testing Native Crashes

Native crash reporters are compiled out of the editor, so native crashes can only be tested in a built player.

{% hint style="warning" %}
`Utils.ForceCrash` goes through Unity's internal crash pipeline and will **not** be captured by the native crash reporters on iOS, macOS, or Android. Use a real native crash, such as a null pointer dereference in native code, to test crash reporting. The **my-unity-crasher** sample uses real native crashes for every platform.
{% endhint %}

### 🧭 Platform Support

What BugSplat captures on each platform:

| Capability                      | Windows              | macOS             | iOS | Android           | Linux | WebGL |
| ------------------------------- | -------------------- | ----------------- | --- | ----------------- | ----- | ----- |
| Managed C# exceptions           | Yes                  | Yes               | Yes | Yes               | Yes   | Yes   |
| Native crashes                  | Yes (Mono or IL2CPP) | Yes (IL2CPP only) | Yes | Yes               | No    | No    |
| Hang / ANR reporting            | Yes (opt-in)         | Yes               | Yes | Yes (Android 11+) | No    | No    |
| Offline retry of native reports | Yes                  | Yes               | Yes | Yes               | n/a   | n/a   |
| User feedback (`PostFeedback`)  | Yes                  | Yes               | Yes | Yes               | Yes   | No    |
| Automatic symbol upload         | Yes                  | Yes               | Yes | Yes               | No    | No    |

* **Managed C# exceptions** are captured on every platform through Unity's log callbacks, including background threads, and posted over HTTPS. WebGL uses a separate reporter that cannot attach log files or screenshots.
* **Native crashes** are captured by default on Windows, macOS (IL2CPP only — a Mono player warns and falls back to managed reporting), iOS, and Android. Each platform has an option to turn it off on your options asset: `UseNativeCrashReportingForWindows`, `UseNativeCrashReportingForMac`, `UseNativeCrashReportingForIos`, and `UseNativeCrashReportingForAndroid`. Native reporters are compiled out of the editor, so play mode exercises managed reporting only.
* **Hang / ANR reporting** is opt-in on Windows through `WindowsHangDetectionTimeoutMs`, and automatic on macOS, iOS, and Android once native crash reporting is enabled. macOS reports a hang only when the frozen app is force-quit.
* **Offline retry** covers native reports only: they are written to disk when the crash happens and uploaded on a later launch. Managed exception posts are never persisted — if that upload fails, the report is gone.
* **Automatic symbol upload** runs as a post-build step and needs [symbol upload credentials](unity/symbol-upload.md). The platform pages below cover what each platform uploads.
* `Post(FileInfo minidump)` posts your own minidump files on every platform except WebGL.

### 📚 More Documentation

| Page                                             | What's in it                                                                                                  |
| ------------------------------------------------ | ------------------------------------------------------------------------------------------------------------- |
| [Windows](unity/windows.md)                      | Native crash reporting, the crash dialog, plugin and IL2CPP symbols, hang detection, Windows Error Reporting  |
| [macOS](unity/macos.md)                          | Native crash reporting, dSYM upload, attachments, hang detection                                              |
| [iOS](unity/ios.md)                              | Native crash reporting, dSYM upload, attachments, hang detection                                              |
| [Android](unity/android.md)                      | Native crash reporting, player settings, `symbols.zip`, ANR reporting                                         |
| [Symbol Upload](unity/symbol-upload.md)          | Credentials, where they resolve from, environment variables                                                   |
| [Scripted Setup](unity/automation.md)            | Configuring BugSplat without the Editor UI, for scripts, CI, and AI agents                                    |
| [Options Reference](unity/api.md)                | Initialization, every `BugSplatOptions` field, `Player.log` and privacy, native attachments                   |
| [Migrating from 4.x](unity/migrating-from-4x.md) | What 5.0.0 removed and renamed, and what to change                                                            |

### 🧑‍💻 Contributing

BugSplat ❤️s open source! If you feel that this package can be improved, please open an [Issue](https://github.com/BugSplat-Git/bugsplat-unity/issues). If you have an awesome new feature you'd like to implement, we'd love to merge your [Pull Request](https://github.com/BugSplat-Git/bugsplat-unity/pulls). You can also send us an [email](mailto:support@bugsplat.com), join us on [Discord](https://discord.gg/K4KjjRV5ve), or message us via the in-app chat on [bugsplat.com](https://bugsplat.com/).
