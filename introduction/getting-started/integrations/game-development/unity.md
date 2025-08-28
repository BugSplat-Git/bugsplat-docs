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

If you're using `BugSplatOptions` and `BugSplatManager`, BugSplat automatically configures an `Application.logMessageReceived` handler that will post reports when it encounters a log message of type `Exception`. You can also extend your BugSplat integration and [customize report metadata](https://github.com/BugSPlat-Git/bugsplat-unity#adding-metadata), [report exceptions in try/catch blocks](https://github.com/BugSPlat-Git/bugsplat-unity#trycatch-reporting), [prevent repeated reports](https://github.com/BugSPlat-Git/bugsplat-unity#preventing-repeated-reports), and [upload windows minidumps](https://github.com/BugSPlat-Git/bugsplat-unity#windows) from native crashes.

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

BugSplat can be configured to upload Windows minidumps created by the `UnityCrashHandler`. BugSplat will automatically pull Unity Player symbols from the [Unity Symbol Server](https://docs.unity3d.com/Manual/WindowsDebugging.html).

The methods `PostCrash`, `PostMostRecentCrash`, and `PostAllCrashes` can be used to upload minidumps to BugSplat. We recommend running `PostAllCrashes` when your game launches.

```csharp
void Start()
{
    bugsplat = FindObjectOfType<BugSplatManager>().BugSplat;
    StartCoroutine(bugsplat.PostAllCrashes());
}
```

Each of the methods that post crashes to BugSplat also accepts a `MinidumpPostOptions` parameter and a `callback`. The usage of `MinidumpPostOptions` and `callback` are nearly identical to the `ExceptionPostOptions` example listed above.

You can generate a test crash on Windows with any of the following methods.

```
Utils.ForceCrash(ForcedCrashCategory.Abort);
Utils.ForceCrash(ForcedCrashCategory.AccessViolation);
Utils.ForceCrash(ForcedCrashCategory.FatalError);
Utils.ForceCrash(ForcedCrashCategory.PureVirtualFunction);
```

#### Windows Symbols

To enable the uploading of plugin symbols, generate an OAuth2 Client ID and Client Secret on the BugSplat [Integrations](https://app.bugsplat.com/v2/settings/database/integrations) page. Add your Client ID and Client Secret to the `BugSplatOptions` object you generated in the [Configuration](https://github.com/BugSPlat-Git/bugsplat-unity#%E2%9A%99%EF%B8%8F-configuration) section. If your game contains Native Windows C++ plugins, `.dll` and `.pdb` files in the `Assets/Plugins/x86` and `Assets/Plugins/x86_64` folders will be uploaded by BugSplat's PostBuild script and used in symbolication.

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

You'll also need to configure the scripting backend to use IL2CPP, and target ARM64 (ARMV7a is not supported)

<figure><img src="../../../../.gitbook/assets/image (70).png" alt=""><figcaption></figcaption></figure>

When you build your app for Android, be sure to set `Create symbols.zip` to `Debugging`

<figure><img src="../../../../.gitbook/assets/image (71).png" alt=""><figcaption></figcaption></figure>

### 🍎 iOS

The bugsplat-unity plugin supports crash reporting for native C++ crashes on iOS via bugsplat-ios. To configure crash reporting for iOS, set the `UseNativeCrashReportingForIos` and `UploadDebugSymbolsForIos` properties to `true` on the BugSplatManager instance.

### 🧩 API

The following API methods are available to help you customize BugSplat to fit your needs.

#### BugSplatManager

| Setting                       | Description                                                                                |
| ----------------------------- | ------------------------------------------------------------------------------------------ |
| DontDestroyManagerOnSceneLoad | Should the BugSplat Manager persist through scene loads?                                   |
| RegisterLogMessageRecieved    | Register a callback function and allow BugSplat to capture instances of LogType.Exception. |

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

### 🧑‍💻 Contributing

BugSplat ❤️s open source! If you feel that this package can be improved, please open an [Issue](https://github.com/BugSplat-Git/bugsplat-unity/issues). If you have an awesome new feature you'd like to implement, we'd love to merge your [Pull Request](https://github.com/BugSplat-Git/bugsplat-unity/pulls). You can also send us an [email](mailto:support@bugsplat.com), join us on [Discord](https://discord.gg/K4KjjRV5ve), or message us via the in-app chat on [bugsplat.com](https://bugsplat.com/).
