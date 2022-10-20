# Unreal Engine

## Overview

BugSplat’s Unreal Engine integration supports most Unreal platforms including desktop computers, the Steam platform, and Linux servers. Support for additional platforms will be provided in the future.

{% hint style="info" %}
BugSplat is developing an Unreal Engine plugin to simplify various configuration steps. This plugin is in **beta** and can be installed by copying files into your game's Plugins folder. To try our BugSplat's Unreal Engine plugin see the [Plugin](unreal-engine.md#unreal-engine-plugin) section below.
{% endhint %}

### Step 1

Package your game, check that the **Include Crash Reporter** and **Include Debug Files** options are selected in your build configuration:

![Integrating Unreal with BugSplat](../../../../.gitbook/assets/unreal-package-project-menu.png)

![UE4\_PackagingSetttings](../../../../.gitbook/assets/unreal-packaging-settings.png)

### Step 2

Sending crash reports to BugSplat can be done via a simple configuration change to Unreal Engine's CrashReportClient. To configure crash upload to your BugSplat database, create a file named DefaultEngine.ini with the following contents:

```
[CrashReportClient]
CrashReportClientVersion=1.0
DataRouterUrl="https://{database}.bugsplat.com/post/ue4/{appName}/{appVersion}"
```

Replace {database}, {appName}, and {appVersion} with the names of your BugSplat database, application name, and version.

{% hint style="info" %}
Remember the values you use for {database}, {appName}, and {appVersion}. You will need to use the same values when uploading [Symbols](../../../development/working-with-symbol-files/) in order to get crash reports with function names and line numbers in the call stack.
{% endhint %}

{% hint style="warning" %}
#### Spaces and special characters in {appName} or {appVersion} might cause unexpected behavior and should be avoided.
{% endhint %}

#### Unreal Engine 4.25 and older

For capturing crashes in packaged games in Unreal Engine 4.25 and earlier, **** copy `DefaultEngine.ini` to `{{output directory}}\Engine\Programs\CrashReportClient\Config\NoRedist` making sure to create folders that don't exist (where`{{output directory}}` is the location of your packaged build).

#### **Unreal Engine 4.26 and newer**

For capturing crashes in packaged games in Unreal Engine 4.26 and newer, copy`DefaultEngine.ini` to `{{output directory}}\Engine\Restricted\NoRedist\Programs\CrashReportClient\Config`  making sure to create folders that don't exist (where`{{output directory}}` is the location of your packaged build).

If DefaultEngine.ini already exists, add the snippet above anywhere in the file. There are multiple DefaultEngine.ini files in your tree, make sure you edit the right one otherwise crash reports will not be sent to BugSplat.

### Step 3

To create symbolic call stacks on Windows platforms you will need to upload symbol and executable files. The easiest way to upload files is to use our `SendPdbs` command line utility. `SendPdbs` can be downloaded either by [clicking here](https://app.bugsplat.com/browse/download\_item.php?item=sendpdbs) or via the [SendPDBs](../../../../education/faq/using-sendpdbs-to-automatically-upload-symbol-files.md) doc. Run it from your build folder using the following commands.

```bash
cd {your build folder}
SendPdbs.exe /u {username} /p {password} /b {database} /a {appName} /v {appVersion} /s /f "*.pdb;*.dll;*.exe"
```

### Step 4

Run your game. For testing, a crash can be forced from the console using the command "debug crash". After posting the crash report, log in to BugSplat to view the report.

### Step 5

Eventually, you will want to rebuild CrashReportClient so that its user interface describes the crash reporting changes above. However, this isn't required to successfully post crash reports.

### Step 6

Special instructions for Linux servers:

* Package the crash reporter with your Linux server build by adding the `-CrashReporter` flag to `PackageBuildLinuxServer.bat`
* Force a test crash by running your server executable with the option `-ExecCmds="debug crash"`

## Custom Fields

We extract metadata from `CrashContext.runtime-xml` file attached to Unreal Engine crash reports. In addition to the values that are provided by prebuilt versions of Unreal Engine, we support a few values our customers have added to their customized engine builds. You can add the following XML fields as child properties of `RuntimeProperties`:

| Name                   | Description                           |
| ---------------------- | ------------------------------------- |
| BugSplatNotes          | A value persisted to the Notes column |
| BugSplatApplicationKey | A value persisted to the Key column   |

## Forwarding Crashes to Epic Games

If you'd like to forward crashes to the original `DataRouterUrl` specified in `DefaultEngine.ini` you can enable the **Forward Crashes** option under the Privacy tab on the [Settings](https://app.bugsplat.com/v2/settings/database/privacy) page. Forwarding crash reports to Epic is useful when a crash in your game is caused by the underlying engine and you are working with Epic Games to resolve the issue. If the Forward to Epic option is enabled, an Epic Correlation-ID will be added to the description of all Unreal Engine crashes that were successfully forwarded to Epic.

## Unreal Engine Plugin

BugSplat is developing an Unreal Engine [plugin](https://github.com/BugSplat-Git/bugsplat-unreal) that simplifies the configuration of crash reporting in Unreal Engine games.&#x20;

BugSplat's Unreal plugin currently supports adding crash reporting to Windows, macOS, Linux, and iOS games. With a few clicks, the BugSplat plugin can be configured to automatically upload symbol files so crash report stack traces display function names and line numbers. Support for Android is coming soon!

To get started with BugSplat's Unreal Engine plugin please check out our [GitHub](https://github.com/BugSplat-Git/bugsplat-unreal) repo. Additionally, an example game, configured with BugSplat's Unreal Engine plugin can be found [here](https://github.com/BugSplat-Git/my-unreal-crasher).

### Install from GitHub

1. Navigate to your project folder, which contains your `[ProjectName].uproject` file.
2. If it does not already exist, create a `Plugins` folder.
3. Create a `BugSplat` folder in the `Plugins` folder and copy the contents of the [bugsplat-unreal](https://github.com/BugSplat-Git/bugsplat-unreal) repo into the `BugSplat` folder.
4. In the Unreal Editor, ensure you can access the BugSplat plugin via `Edit > Project Settings` and scrolling to the `BugSplat` section under `Plugins`.

### Configuration

BugSplat's Unreal plugin currently supports adding crash reporting to Windows, macOS, Linux, and iOS games. With a few clicks, the BugSplat plugin can be configured to automatically upload symbol files so crash report stack traces display function names and line numbers. Support for Android is coming soon!

To get started, access the BugSplat plugin menu in the Unreal Editor via `Edit > Project Settings`. Scroll to the `BugSplat` section of `Project Settings` and add values for `Database`, `Application`, `Version`, `User`, and `Password`:

![](<../../../../.gitbook/assets/image (8).png>)

#### Desktop

For Desktop, the BugSplat plugin has the ability to modify the [DefaultEngine.ini](https://docs.unrealengine.com/5.0/en-US/configuration-files-in-unreal-engine/) file for both a packaged build or the global engine so that crashes are posted to BugSplat. Additionally, the BugSplat plugin can add a [PostBuildStep](https://docs.unrealengine.com/5.0/en-US/unreal-engine-build-tool-target-reference/) that will upload Windows [symbol files](https://docs.bugsplat.com/introduction/development/working-with-symbol-files) to BugSplat after each build.

BugSplat recommends configuring the crash reporting independently for each packaged build. To configure crash reporting in a packaged build select `Update Game INI`. When prompted, navigate to the root directory of your packaged build that contains the folder `Windows` or `WindowsNoEditor`. Note that you will need to **repeat this step and update the version information every time you create a packaged version of your game**. For production scenarios consider automating this step with a script on your build machine.

![](<../../../../.gitbook/assets/image (7).png>)

Alternatively, BugSplat can be configured to collect crash reports in the editor and all games built with the current engine. To configure BugSplat for the current engine, select `Update Global INI`. Note that updating the global `DefaultEngine.ini` file **will affect all projects using the same engine build**.

In order to get function names and line numbers in crash reports you'll need to upload your game's `.exe`, `.dll`, and `.pdb` files. To upload symbol files to BugSplat, select the `Add Symbol Uploads` button. The `Add Symbol Uploads` button will generate a bash script which uploads your project's [symbol files](https://docs.bugsplat.com/introduction/development/working-with-symbol-files) to BugSplat. A script to execute [SendPdbs.exe](https://docs.bugsplat.com/education/faq/using-sendpdbs-to-automatically-upload-symbol-files) will be added to the `PostBuildSteps` field in `BugSplat.uplugin` and will run automatically when your game is built.

#### Mobile

Installing the BugSplat plugin will configure crash reporting and symbol uploads automatically on mobile platforms.

Before attempting to use the BugSplat plugin to capture crashes on Mobile please ensure you've completed the [iOS](https://docs.unrealengine.com/5.0/en-US/setting-up-an-unreal-engine-project-for-ios/) and [Android](https://docs.unrealengine.com/5.0/en-US/android-support-for-unreal-engine/) quickstart guides.

In order to get function names and line numbers in your iOS crash reports, please make the following changes in the `iOS` section of `Project Settings`

| Option                                                 | Value |
| ------------------------------------------------------ | ----- |
| Generate dSYMs for code debugging and profiling        | true  |
| Generate dSYMs as a bundle for third party crash tools | true  |
| Support bitcode in shipping                            | false |

Note that sometimes iOS applications won't crash while the USB cable is connected. If this happens, disconnect the USB cable and re-run the application to trigger a crash.

### Usage

Once you've installed the plugin, add the following C++ snippet to your game to generate a sample crash.

```
UE_LOG(LogTemp, Fatal, TEXT("BugSplat!"));
```

Run your application and submit a crash report.

On Desktops, submit a crash report via the Unreal CrashReportClient dialog that appears at crash time. We have developed a handy guide on how you can customize the Unreal CrashReportClient dialog that is available [here](https://www.bugsplat.com/blog/game-dev/customizing-unreal-engine-crash-dialog/).

On iOS, after a crash occurs, restart the game and tap the `Send Report` option when prompted.

Once you've submitted a crash report navigate to the [Crashes](https://app.bugsplat.com/v2/crashes) page. On the Crashes page, click the link in the ID column.

If everything is configured correctly, you should see something that resembles the following:

\
![](<../../../../.gitbook/assets/image (4).png>)

### Contributing

BugSplat ❤️s open source! If you feel that this package can be improved, please open an [Issue](https://github.com/BugSplat-Git/bugsplat-unreal/issues). If you have an awesome new feature you'd like to implement, we'd love to merge your [Pull Request](https://github.com/BugSplat-Git/bugsplat-unreal/pulls). You can also send us an [email](mailto:support@bugsplat.com), join us on [Discord](https://discord.gg/K4KjjRV5ve), or message us via the in-app chat on [bugsplat.com](https://bugsplat.com/).
