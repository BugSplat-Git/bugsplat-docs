# Unreal Engine

## Introduction

Before integrating a new BugSplat SDK with your application, make sure to review the [Getting Started](https://www.bugsplat.com/resources/bugsplat-101/) resources and complete the simple startup tasks listed below.

* [Sign up](https://app.bugsplat.com/v2/sign-up) for a BugSplat account
* [Log in](https://app.bugsplat.com/auth0/login) using your email address
* Create a new [database](https://app.bugsplat.com/v2/company) for your application

{% hint style="info" %}
**Need any further help?** Check out the full BugSplat documentation [here](https://www.bugsplat.com/docs), or email the team at [support@bugsplat.com](mailto:support@bugsplat.com).
{% endhint %}

## Overview

BugSplat’s Unreal Engine integration supports most Unreal platforms including desktop computers, the Steam platform, and Linux servers. Support for additional platforms will be provided in the future.

#### Step 1

To send crash reports to BugSplat, change the `DataRouterUrl` parameter in `/Engine/Programs/CrashReportClient/Config/NoRedist/DefaultEngine.ini` so that crash reports are posted to our back end. There are multiple DefaultEngine.ini files in your tree, make sure you edit the right one.

Here's what the crash report client section of `DefaultEngine.ini` should look like:

```text
[CrashReportClient]
CrashReportClientVersion=1.0
DataRouterUrl="https://{database}.bugsplat.com/post/ue4/{appName}/{appVersion}"
```

Replace {database}, {appName}, and {appVersion} with the names of your BugSplat database, application name, and version. \(You will use these exact same parameter values when uploading symbols

#### Step 2

Package your game, check that the **Include Crash Reporter** and **Include Debug Files** options are selected in your build configuration:

![Integrating Unreal with BugSplat](https://www.bugsplat.com/assets/img/docs/unreal2.png)

![UE4\_PackagingSetttings](https://www.bugsplat.com/assets/img/docs/UE4_PackagingSetttings.png)

#### Step 3

To create symbolic call stacks on Windows platforms you will need to upload symbol and executable files. The easiest way to upload files is to use our `SendPdbs` command line utility. `SendPdbs` can be downloaded either by [clicking here](https://app.bugsplat.com/browse/download_item.php?item=sendpdbs) or via the [SendPDBs](https://www.bugsplat.com/docs/faq/sendpdbs) doc. Run it from your build folder using the following commands.

```bash
cd {your build folder}
SendPdbs.exe /u {username} /p {password} /b {database} /a {appName} /v {appVersion} /s /f "*.pdb;*.dll;*.exe"
```

#### Step 4

Run your game. For testing, a crash can be forced from the console using the command "debug crash". After posting the crash report, login to BugSplat to view the report.

#### Step 5

Eventually, you will want to rebuild CrashReportClient so that its user interface describes the crash reporting changes above. However, this isn't required to successfully post crash reports.

#### Step 6

Special instructions for Linux servers:

* Package the crash reporter with your Linux server build by adding the `-CrashReporter` flag to `PackageBuildLinuxServer.bat`
* Force a test crash by running your server executable with the option `-ExecCmds="debug crash"`

## Forwarding Crashes to Epic Games

If you'd like to forward crashes to the original `DataRouterUrl` specified in `DefaultEngine.ini` you can enable the **Forward Crashes** option under the Privacy tab on the [Options](https://app.bugsplat.com/v2/options?tab=privacy) page. Forwarding crash reports to Epic is useful when a crash in your game is caused by the underlying engine and you are working with Epic Games to resolve the issue. If the Forward to Epic option is enabled, an Epic Correlation-ID will be added to the description of all Unreal Engine crashes that were successfully forwarded to Epic.

