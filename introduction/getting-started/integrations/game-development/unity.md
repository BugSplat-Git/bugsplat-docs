# Unity

## Overview

BugSplat's [`com.bugsplat.unity`](https://openupm.com/packages/com.bugsplat.unity/?subPage=readme) package provides crash and exception reporting for Unity projects. BugSplat provides you with invaluable insight into the issues tripping up your users. Our Unity integration collects screenshots, log files, exceptions, and Windows minidumps so that you can fix bugs and deliver a better user experience.

## Installation

BugSplat's `com.bugsplat.unity` package can be added to your project via [OpenUPM](https://openupm.com/packages/com.bugsplat.unity/?subPage=readme) or a [URL](https://github.com/BugSplat-Git/bugsplat-unity) to our git repository.

### OpenUPM

Information on how to install the [OpenUPM](https://openupm.com/) package for [Node.js](https://nodejs.org/en/) can be found [here](https://openupm.com/).

```shell
openupm add com.bugsplat.unity
```

### Git

Information on adding a Unity package via a git URL can be found [here](https://docs.unity3d.com/Manual/upm-ui-giturl.html).

## Sample

After installing `com.bugsplat.unity` you'll have the opportunity to import a sample project that's fully configured to post error reports to BugSplat. Click here if you'd like to skip the sample project and get straight to the [usage](https://openupm.com/packages/com.bugsplat.unity/?subPage=readme#usage) instructions.

To import the sample, click the carrot next to **Samples** to reveal the **my-unity-crasher** sample. Click **Import** to add the sample to your project.

![Importing the Sample](https://bugsplat-public.s3.amazonaws.com/unity/import-sample.png)

In the Project Assets browser, open the **Sample** scene from `Samples > BugSplat > Version > my-unity-crasher > Scenes`.

Next, select `Samples > BugSplat > Version > my-unity-crasher` to reveal the **BugSplatOptions** object. Click BugSplatOptions and replace the value of database with your BugSplat database.

![Finding the Sample](https://bugsplat-public.s3.amazonaws.com/unity/bugsplat-options.png)

![Configuring BugSplat](https://bugsplat-public.s3.amazonaws.com/unity/bugsplat-manager.png)

Click **Play** and click or tap one of the buttons to send an error report to BugSplat. To view the error report, navigate to the BugSplat \[Dashboard] ([https://app.bugsplat.com/v2/dashboard](https://app.bugsplat.com/v2/dashboard)) and ensure that you have the correct database selected.

![Running the Sample](https://bugsplat-public.s3.amazonaws.com/unity/sample-scene.png)

## Configuration

BugSplat's Unity integration is flexible and can be used in a variety of ways. The easiest way to get started is to attach the `BugSplatManager` Monobehaviour to a GameObject.

![BugSplat Manager](https://bugsplat-public.s3.amazonaws.com/unity/BugSplatManager.png)

`BugSplatManager` needs be initialized with a `BugSplatOptions` serialized object. A new instance of `BugSplatOptions` can be created through the Asset create menu.

![BugSplat Create Options](https://bugsplat-public.s3.amazonaws.com/unity/BugSplatOptions.png)

Configure fields as appropriate. Note that if Application or Version are left empty, `BugSplat` will by default configure these values with `Application.productName` and `Application.version`, respectively.

![BugSplat Options](https://bugsplat-public.s3.amazonaws.com/unity/BugSplatOptionsObject.png)

Finally, provide a valid `BugSplatOptions` to `BugSplatManager`.

![BugSplat Manager Configured](https://bugsplat-public.s3.amazonaws.com/unity/ConfiguredBugSplatManager.png)

### BugSplat Manager <a href="#bugsplat-manager-settings" id="bugsplat-manager-settings"></a>

| Setting                           | Description                                                                                |
| --------------------------------- | ------------------------------------------------------------------------------------------ |
| **DontDestroyManagerOnSceneLoad** | Should the BugSplat Manager persist through scene loads?                                   |
| **RegisterLogMessageRecieved**    | Register a callback function and allow BugSplat to capture instances of LogType.Exception. |

### BugSplat Options <a href="#bugsplat-options" id="bugsplat-options"></a>

| Option                                | Description                                                                                                                                                                                                                                            |
| ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Database**                          | The name of your BugSplat database.                                                                                                                                                                                                                    |
| **Application**                       | The name of your BugSplat application. Defaults to Application.productName if no value is set.                                                                                                                                                         |
| **Version**                           | The version of your BugSplat application. Defaults to Application.version if no value is set.                                                                                                                                                          |
| **Description**                       | A default description that can be overridden by call to Post.                                                                                                                                                                                          |
| **Email**                             | A default email that can be overridden by call to Post.                                                                                                                                                                                                |
| **Key**                               | A default key that can be overridden by call to Post.                                                                                                                                                                                                  |
| **User**                              | A default user that can be overridden by call to Post                                                                                                                                                                                                  |
| **CaptureEditorLog**                  | Should BugSplat upload Editor.log when Post is called                                                                                                                                                                                                  |
| **CapturePlayerLog**                  | Should BugSplat upload Player.log when Post is called                                                                                                                                                                                                  |
| **CaptureScreenshots**                | Should BugSplat a screenshot and upload it when Post is called                                                                                                                                                                                         |
| **PostExceptionsInEditor**            | Should BugSplat upload exceptions when in editor                                                                                                                                                                                                       |
| **PersistentDataFileAttachmentPaths** | Paths to files (relative to Application.persistentDataPath) to upload with each report                                                                                                                                                                 |
| **SymbolUploadClientId**              | An OAuth2 Client ID value used for uploading [symbol files](https://docs.bugsplat.com/introduction/development/working-with-symbol-files) generated via BugSplat's [Integrations](https://app.bugsplat.com/v2/settings/database/integrations) page     |
| **SymbolUploadClientSecret**          | An OAuth2 Client Secret value used for uploading [symbol files](https://docs.bugsplat.com/introduction/development/working-with-symbol-files) generated via BugSplat's [Integrations](https://app.bugsplat.com/v2/settings/database/integrations) page |

### **Further Customization** <a href="#configuring-in-code" id="configuring-in-code"></a>

If your application requires special configuration, you may optionally create your own script to manage and instantiate `BugSplat`. To do so, create a new script and attach it to a GameObject. In your script, add a using statement for BugSplatUnity.

```csharp
using BugSplatUnity;
```

Next, create a new instance of `BugSplat` passing it your `database`, `application`, and `version`. Use `Application.productName`, and `Application.version` for application and version respectively.

```csharp
var bugsplat = new BugSplat(database, Application.productName, Application.version);
```

You can set the defaults for a variety of properties on the `BugSplat` instance. These default values will be used in exception and crash posts. Additionally, you can tell BugSplat to capture a screenshot, include the Player.log file, and include the Editor.log file when an exception is recorded.

```csharp
bugsplat.Attachments.Add(new FileInfo("/path/to/attachment.txt"));
bugsplat.Description = "description!";
bugsplat.Email = "fred@bugsplat.com";
bugsplat.Key = "key!";
bugsplat.User = "Fred";
bugsplat.CaptureEditorLog = true;
bugsplat.CapturePlayerLog = false;
bugsplat.CaptureScreenshots = true;
```

Alternatively, a new instance of BugSplat can be created with `BugSplatOptions`.

```csharp
[SerializeField]
BugSplatOptions bugSplatOptions;
...
var bugsplat = BugSplat.CreateFromOptions(bugSplatOptions);
```

## Usage <a href="#usage" id="usage"></a>

First, find your BugSplat instance. For example, using `BugSplatManager`:

```csharp
var bugsplat = FindObjectOfType<BugSplatManager>().BugSplat;
```

You can send exceptions to BugSplat in a try/catch block by calling `Post`.

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
    User = "Barney"
};

options.AdditionalAttachments.Add(new FileInfo("/path/to/additional.txt"));

static void callback()
{
    Debug.Log($"Exception post callback!");
};

StartCoroutine(bugsplat.Post(ex, options, callback));
```

You can also configure a global `LogMessageRecieved` callback. When the BugSplat instance recieves a logging event where the type is `Exception` it will upload the exception. Note that the `BugSplatManager` can be configured to register this callback at startup.

```csharp
Application.logMessageReceived += bugsplat.LogMessageReceived;
```

### Windows Minidumps <a href="#windows-minidumps" id="windows-minidumps"></a>

BugSplat can be configured to upload Windows minidumps created by the `UnityCrashHandler`. BugSplat will automatically pull Unity Player symbols from the [Unity Symbol Server](https://docs.unity3d.com/Manual/WindowsDebugging.html). If your game contains Native Windows C++ plugins, `.dll` and `.pdb` files in the `Assets/Plugins/x86` and `Assets/Plugins/x86_64` folders will be uploaded automatically by BugSplat's PostBuild script and used in symbolication.

To enable uploading of plugin symbols, generate a Client ID and Client Secret on the BugSplat [Integrations](https://app.bugsplat.com/v2/settings/database/integrations) page. Add your Client ID and Client Secret to the `BugSplatOptions` object you generated in the [Configuration](https://openupm.com/packages/com.bugsplat.unity/?subPage=readme#%E2%9A%99%EF%B8%8F-configuration) section. Once configured, plugins will be uploaded automatically the next time you build your project.

The methods `PostCrash`, `PostMostRecentCrash`, and `PostAllCrashes` can be used to upload minidumps to BugSplat. We recommend running `PostMostRecentCrash` when your game launches.

```csharp
StartCoroutine(bugsplat.PostCrash(new FileInfo("/path/to/crash/folder")));
StartCoroutine(bugsplat.PostMostRecentCrash());
StartCoroutine(bugsplat.PostAllCrashes());
```

Each of the methods that post crashes to BugSplat also accept a `MinidumpPostOptions` parameter and a `callback`. The usage of `MinidumpPostOptions` and `callback` are nearly identically to the `ExceptionPostOptions` example listed above.

You can generate a test crash on Windows with any of the following methods.

```csharp
Utils.ForceCrash(ForcedCrashCategory.Abort);
Utils.ForceCrash(ForcedCrashCategory.AccessViolation);
Utils.ForceCrash(ForcedCrashCategory.FatalError);
Utils.ForceCrash(ForcedCrashCategory.PureVirtualFunction);
```

Once you've posted an exception or a minidump to BugSplat click the link in the **ID** column on either the [Dashboard](https://app.bugsplat.com/v2/dashboard) or [Crashes](https://app.bugsplat.com/v2/crashes) pages to see details about your crash.

<figure><img src="../../../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

### UWP <a href="#uwp" id="uwp"></a>

In order to use BugSplat in a Universal Windows Platform application you will need to add some capabilities to the `Package.appxmanifest` file in the solution directory that Unity generates at build time.

#### Exceptions and Log Files <a href="#exceptions-and-log-files" id="exceptions-and-log-files"></a>

In order to report exceptions and upload log files you will need to add the `Internet (Client)` capability.

#### Windows Minidumps <a href="#windows-minidumps-1" id="windows-minidumps-1"></a>

To upload minidumps created on Windows you will need to add the `Internet (Client)` capability.

Additionally, we found there were some restricted capabilities that were required in order to generate minidumps. Please see this Microsoft [document](https://docs.microsoft.com/en-us/windows/win32/wer/collecting-user-mode-dumps) that describes how to configure your system to generate minidumps for UWP native crashes.

To upload minidumps from `%LOCALAPPDATA%\CrashDumps` you will also need to add the `broadFileSystemAccess` restricted capability. To add access to the file system you will need to add the following to your `Package.appxmanifest`:

```xml
<Package xmlns:rescap="http://schemas.microsoft.com/appx/manifest/foundation/windows10/restrictedcapabilities" ... >
```

Under the `Capabilities` section add the `broadFileSystemAccess` capability:

```xml
<Capabilities>
    <rescap:Capability Name="broadFileSystemAccess" />
</Capabilities>
```

Finally, ensure that your application has access to the file system. The following is a snippet illustrating where this is set for our [my-unity-crasher](https://github.com/BugSplat-Git/my-unity-crasher) sample:

<figure><img src="../../../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

### WebGL

A few settings must be changed in order to capture function names and line numbers in a WebGL exception report. Open **File > Build Settings** and switch the platform to **WebGL** and check **Development Build.**

![Unity Build Settings Menu](../../../../.gitbook/assets/unity-build-settings.png)

Click the button that says **Player Settings**, highlight the **Player** section. Under **Publishing Settings**, select **Full With Stacktrace**.&#x20;

![Unity Player Publishing Settings](../../../../.gitbook/assets/unity-full-with-stack-trace.png)

Please note that Unity does not recommend shipping development builds and thus this configuration is not recommended for applications in production.

## Contributing

BugSplat ❤️ 's open source! If you feel that this integration can be improved, please open an [Issue](https://github.com/BugSplat-Git/bugsplat-unity/issues). If you have an awesome new feature you'd like to implement, we'd love to merge your [Pull Request](https://github.com/BugSplat-Git/bugsplat-unity/pulls). You can also reach out to us via an email to [support@bugsplat.com](mailto:support@bugsplat.com) or the in-app chat on bugsplat.com.
