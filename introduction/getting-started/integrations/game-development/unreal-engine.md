# Unreal Engine

{% hint style="info" %}
If you are a developer looking to try BugSplat on your local machine, we've created a [Plugin](unreal-engine/unreal-engine-plugin.md) to help you get started.
{% endhint %}

BugSplat’s Unreal Engine integration supports capturing crashes on Windows, macOS, Linux, iOS, Android, Xbox, and PlayStation games. Support for the Nintendo Switch is coming soon. BugSplat also supports both Windows and Linux servers.

There are two options for configuring BugSplat. If you are integrating BugSplat for an organization that uses a dedicated build machine, please continue reading.&#x20;

## Windows 🪟

You must upload symbol and executable files to create symbolic call stacks on Windows platforms. The easiest way to upload files is to use our [symbol-upload](../../../../education/faq/how-to-upload-symbol-files-with-symbol-upload.md) command line utility. You can download `symbol-upload` [here](https://github.com/BugSplat-Git/symbol-upload/releases).

### Symbol Uploads

Add a step to your build pipeline that uploads `.exe`, `.dll`, and `.pdb` files via [symbol-upload](../../../../education/faq/how-to-upload-symbol-files-with-symbol-upload.md). We recommend creating an [OAuth Client ID/Client Secret](../../../development/web-services/oauth2.md#client-credentials) pair for authentication.

```bash
cd {your build folder}
symbol-upload-windows.exe -i {client id} -s {client secret} -b {database} -a {appName} -v {appVersion} -f "*.pdb;*.dll;*.exe"
```

The `appName` and `appVersion` parameters will be associated with your uploaded symbols, allowing logical grouping of files within BugSplat for easier symbol management. BugSplat will automatically remove symbol stores that have not been accessed recently. See our [FAQ](../../../../education/faq/how-do-i-remove-symbol-files.md#automatically) for a description of these rules.

### Packaging Settings

Package your game, and check the **Include Crash Reporter** and **Include Debug Files** options are selected in your build configuration:

![Integrating Unreal with BugSplat](../../../../.gitbook/assets/unreal-package-project-menu.png)

![UE4\_PackagingSetttings](../../../../.gitbook/assets/unreal-packaging-settings.png)

### Configuring Crash Report Client

Sending crash reports to BugSplat can be done via a simple configuration change to Unreal Engine's [CrashReportClient](https://blog.bugsplat.com/customizing-the-unreal-engine-crash-report-client/).

There are two options for configuring CrashReportClient. The first option is modifying the `DefaultEngine.ini` file for an engine install or source build. Modifying the engine's config file is the easiest approach but has the downside of affecting every game you build with this version. Alternatively, you can add `DefaultEngine.ini` to a specific path in your packaged build directory, which will overwrite values specified by the engine.

#### Option 1: Editor and Engine Crashes

Unreal Editor can also be configured to send crash reports to BugSplat. To do this, add the following DefaultEngine.ini file in `C:\Path\To\Engine_Install_or_Source_Checkout\Programs\CrashReportClient\Config\DefaultEngine.ini.`

```
[CrashReportClient]
CrashReportClientVersion=1.0
DataRouterUrl="https://{database}.bugsplat.com/post/ue4/{appName}/{appVersion}"
```

#### Option 2: Packaged Builds

To configure crash uploads to your BugSplat database, create a file named `DefaultEngine.ini` with the following contents:

```
[CrashReportClient]
CrashReportClientVersion=1.0
DataRouterUrl="https://{database}.bugsplat.com/post/ue4/{appName}/{appVersion}"
```

Replace `{database}`, `{appName}`, and `{appVersion}` with the names of your BugSplat database, application name, and version. The appName and appVersion parameters will be assigned to each crash report posted to your database, allowing you to group and filter crashes within BugSplat. These parameters should match the corresponding values used for SendPdbs.

{% hint style="warning" %}
**Be sure to URL encode spaces and special characters in {appName} or {appVersion}**
{% endhint %}

#### Unreal Engine 4.25 and older

For capturing crashes in packaged games in Unreal Engine 4.25 and earlier, copy `DefaultEngine.ini` to `{{output directory}}\Engine\Programs\CrashReportClient\Config\NoRedist` making sure to create folders that don't exist (where`{{output directory}}` is the location of your packaged build).

#### **Unreal Engine 4.26 and newer**

For capturing crashes in packaged games in Unreal Engine 4.26 and newer, copy`DefaultEngine.ini` to `{{output directory}}\Engine\Restricted\NoRedist\Programs\CrashReportClient\Config` making sure to create folders that don't exist (where`{{output directory}}` is the location of your packaged build).

If `DefaultEngine.ini` already exists, add the snippet above anywhere in the file. There are multiple `DefaultEngine.ini` files in your tree. Ensure you edit the right DefaultEngine.ini file; otherwise, crash reports will not be sent to BugSplat.

### Trigger a Crash

Run your game. For testing, a crash can be forced from the console using the command "debug crash". After posting the crash report, log in to BugSplat to view the report.

### Optional: Customize Crash Report Client

The default CrashReportClient contains text explaining to the user that the crash reports are being sent to Epic. However, by overwriting the CrashReportClient configuration settings, crash reports are instead sent to BugSplat. It is a good idea to change the text and rebuild CrashReportClient. Customizing CrashReportClient requires rebuilding the engine source.

Please see our [blog](https://blog.bugsplat.com/customizing-the-unreal-engine-crash-report-client/) for more information on how to [customize Unreal's CrashReportClient](https://blog.bugsplat.com/customizing-the-unreal-engine-crash-report-client/).

## Linux Servers 🐧

Special instructions for Linux servers:

* Package the crash reporter with your Linux server build by adding the `-CrashReporter` flag to `PackageBuildLinuxServer.bat`
* Force a test crash by running your server executable with the option `-ExecCmds="debug crash"`

Symbolic call stacks are resolved if you deploy symbols on your server. This is the typical case. However, if symbols aren't available locally, upload the Unreal Linux custom symbol files (`.psym` extension) using [symbol-upload](../../../../education/faq/how-to-upload-symbol-files-with-symbol-upload.md).

## iOS 🍎

You will need to configure [bugsplat-ios](../mobile/ios.md) to capture iOS crash reports. Additionally, you'll need to upload `.dSYM` files for function names and line numbers to be included in crash reports. Symbol files can be uploaded automatically by invoking [symbol-upload](../../../../education/faq/how-to-upload-symbol-files-with-symbol-upload.md).

## Android 🤖

You will need to configure [Crashpad](../mobile/android.md) to capture Android crash reports. Additionally, you'll need to generate symbol files from your `.so` files for function names and line numbers to be included in crash reports. Symbol files can be generated and uploaded automatically by invoking [symbol-upload](../../../../education/faq/how-to-upload-symbol-files-with-symbol-upload.md) with the `-m` flag.

## Licensee Builds 🤝

Some forks of Unreal (e.g., Oculus) are set up as a "Licensee" build. Regardless of other settings, crash reports won't be sent because of this [block of code](https://github.com/EpicGames/UnrealEngine/blob/5ccd1d8b91c944d275d04395a037636837de2c56/Engine/Source/Runtime/Core/Private/Unix/UnixPlatformCrashContext.cpp#L594-L600):

```cpp
if (BuildSettings::IsLicenseeVersion() && !UD_EDITOR)
{
    // do not send unattended reports in licensees' builds except for the editor, where it is governed
    bSendUnattendedBugReports = false;
    bAgreeToCrashUpload = false;
    bSendUsageData = false;
}
```

To upload crash reports to BugSplat, recompile with `bSendUnattendedBugReports = true`.

## Custom Fields 📝

We extract metadata from the `CrashContext.runtime-xml` file attached to Unreal Engine crash reports and convert those values into crash attributes that can be displayed in the web application.  Customers can add their own data to this file by creating a custom Unreal Engine build.  See the [Epic crash reporter documentation](https://dev.epicgames.com/documentation/en-us/unreal-engine/crash-reporting-in-unreal-engine) for information on adding additional data. &#x20;

The following XML fields, if created as child properties of `RuntimeProperties,`can be used to set the BugSplat crash Notes and Key fields

| XML Tag Name              | Description                              |
| ------------------------- | ---------------------------------------- |
| \<BugSplatNotes>          | Sets the value of the crash Notes field  |
| \<BugSplatApplicationKey> | Sets the value of the crash Key field    |

## Forwarding Crashes to Epic Games 📤

If you'd like to forward crashes to the original `DataRouterUrl` specified in `DefaultEngine.ini` you can enable the **Forward Crashes** option under the Privacy tab on the [Settings](https://app.bugsplat.com/v2/database/privacy) page. Forwarding crash reports to Epic is useful when the underlying engine causes a crash in your game, and you are working with Epic Games to resolve the issue. If the Forward to Epic option is enabled, an Epic Correlation-ID will be added to the description of all Unreal Engine crashes successfully forwarded to Epic.
