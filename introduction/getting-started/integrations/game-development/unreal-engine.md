# Unreal Engine

BugSplat’s Unreal Engine integration supports most Unreal platforms including desktop computers, the Steam platform, and Linux servers. Support for additional platforms will be provided in the future.

There are two options for configuring BugSplat. If you are integrating BugSplat on behalf of your organization that uses a dedicated build machine, please continue reading. If you are a developer who is looking to try BugSplat on your local machine you can skip to the [Plugin](unreal-engine/unreal-engine-plugin.md) section below.

Additionally, if you're looking to integrate BugSplat on iOS and Android, please refer to our [Plugin](unreal-engine/unreal-engine-plugin.md) page.

## Windows 🪟

To create symbolic call stacks on Windows platforms, you must upload symbol and executable files. The easiest way to upload files is to use our `SendPdbs` command line utility. `SendPdbs` can be downloaded either by [clicking here](https://app.bugsplat.com/browse/download\_item.php?item=sendpdbs) or via the [SendPDBs](../../../../education/faq/using-sendpdbs-to-automatically-upload-symbol-files.md) doc.

### Symbol Uploads

Add a step to your build pipeline that uploads `.exe`, `.dll`, and `.pdb` files via [SendPdbs](https://app.bugsplat.com/browse/download\_item.php?item=sendpdbs).

```bash
cd {your build folder}
SendPdbs.exe /u {username} /p {password} /b {database} /a {appName} /v {appVersion} /s /f "*.pdb;*.dll;*.exe"
```

The `appName` and `appVersion` parameters will be associated with your uploaded symbols, creating a **symbol store**. BugSplat will automatically remove symbol stores that have not been accessed recently. See the [SendPDBs](../../../../education/faq/using-sendpdbs-to-automatically-upload-symbol-files.md) doc for a description of these rules.

### Packaging Settings

Package your game, and check the **Include Crash Reporter** and **Include Debug Files** options are selected in your build configuration:

![Integrating Unreal with BugSplat](../../../../.gitbook/assets/unreal-package-project-menu.png)

![UE4\_PackagingSetttings](../../../../.gitbook/assets/unreal-packaging-settings.png)

### Configuring Crash Report Client

Sending crash reports to BugSplat can be done via a simple configuration change to Unreal Engine's [CrashReportClient](https://blog.bugsplat.com/customizing-the-unreal-engine-crash-report-client/).

There are two options for configuring CrashReportClient. The first option is modifying the `DefaultEngine.ini` file for an engine install or engine source checkout. Modifying the engine's config file is the easiest approach but has the downside of affecting every game that you build with this version of the engine. Alternatively, you can add `DefaultEngine.ini` to a specific path in your packaged build directory which will overwrite values specified by the engine.

#### Option 1: Editor and Engine Crashes

Unreal Editor can also be configured to send crash reports to BugSplat. To do this, add the following DefaultEngine.ini file in `C:\Path\To\Engine_Install_or_Source_Checkout\Programs\CrashReportClient\Config\DefaultEngine.ini.`&#x20;

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

Replace `{database}`, `{appName}`, and `{appVersion}` with the names of your BugSplat database, application name, and version.  The appName and appVersion parameters will be assigned to each crash report posted to your database, allowing you to group and filter crashes within BugSplat.  These parameters should match the corresponding values used for SendPdbs.

{% hint style="warning" %}
#### Spaces and special characters in {appName} or {appVersion} might cause unexpected behavior and should be avoided.
{% endhint %}

#### Unreal Engine 4.25 and older

For capturing crashes in packaged games in Unreal Engine 4.25 and earlier, copy `DefaultEngine.ini` to `{{output directory}}\Engine\Programs\CrashReportClient\Config\NoRedist` making sure to create folders that don't exist (where`{{output directory}}` is the location of your packaged build).

#### **Unreal Engine 4.26 and newer**

For capturing crashes in packaged games in Unreal Engine 4.26 and newer, copy`DefaultEngine.ini` to `{{output directory}}\Engine\Restricted\NoRedist\Programs\CrashReportClient\Config`  making sure to create folders that don't exist (where`{{output directory}}` is the location of your packaged build).

If `DefaultEngine.ini` already exists, add the snippet above anywhere in the file. There are multiple `DefaultEngine.ini` files in your tree, make sure you edit the right one otherwise crash reports will not be sent to BugSplat.

### Trigger a Crash

Run your game. For testing, a crash can be forced from the console using the command "debug crash". After posting the crash report, log in to BugSplat to view the report.

### Optional: Customize Crash Report Client

The default CrashReportClient contains text that explains to the user that the crash reports are being sent to Epic. By overwriting the CrashReportClient configuration settings crash reports are instead sent to BugSplat. It is a good idea to change the text and rebuild CrashReportClient. Customizing CrashReportClient requires rebuilding the engine source.

Please see our [blog](https://blog.bugsplat.com/customizing-the-unreal-engine-crash-report-client/) for more information on how to [customize Unreal's CrashReportClient](https://blog.bugsplat.com/customizing-the-unreal-engine-crash-report-client/).

## Linux Servers 🐧

Special instructions for Linux servers:

* Package the crash reporter with your Linux server build by adding the `-CrashReporter` flag to `PackageBuildLinuxServer.bat`
* Force a test crash by running your server executable with the option `-ExecCmds="debug crash"`

## Custom Fields 📝

We extract metadata from `CrashContext.runtime-xml` file attached to Unreal Engine crash reports. In addition to the values that are provided by prebuilt versions of Unreal Engine, we support a few values our customers have added to their customized engine builds. You can add the following XML fields as child properties of `RuntimeProperties`:

| Name                   | Description                           |
| ---------------------- | ------------------------------------- |
| BugSplatNotes          | A value persisted to the Notes column |
| BugSplatApplicationKey | A value persisted to the Key column   |

## Forwarding Crashes to Epic Games 📤

If you'd like to forward crashes to the original `DataRouterUrl` specified in `DefaultEngine.ini` you can enable the **Forward Crashes** option under the Privacy tab on the [Settings](https://app.bugsplat.com/v2/settings/database/privacy) page. Forwarding crash reports to Epic is useful when a crash in your game is caused by the underlying engine and you are working with Epic Games to resolve the issue. If the Forward to Epic option is enabled, an Epic Correlation-ID will be added to the description of all Unreal Engine crashes that were successfully forwarded to Epic.
