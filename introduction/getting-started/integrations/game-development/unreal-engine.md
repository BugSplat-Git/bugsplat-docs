# Unreal Engine

## Overview

BugSplat’s Unreal Engine integration supports most Unreal platforms including desktop computers, the Steam platform, and Linux servers. Support for additional platforms will be provided in the future.

### Step 1

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

### Step 2

Package your game, check that the **Include Crash Reporter** and **Include Debug Files** options are selected in your build configuration:

![Integrating Unreal with BugSplat](../../../../.gitbook/assets/unreal-package-project-menu.png)

![UE4\_PackagingSetttings](../../../../.gitbook/assets/unreal-packaging-settings.png)

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
