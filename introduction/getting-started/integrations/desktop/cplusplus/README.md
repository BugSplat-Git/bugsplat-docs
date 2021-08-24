# Windows \(Native C++\)

## Introduction

Before integrating a new BugSplat SDK with your application, make sure to review the [Getting Started](../../../) resources and complete the simple startup tasks listed below.

* [Sign up](https://app.bugsplat.com/v2/sign-up) for a BugSplat account
* [Log in](https://app.bugsplat.com/auth0/login) using your email address
* Create a new [database](https://app.bugsplat.com/v2/company) for your application

{% hint style="info" %}
Need any further help? Check out the full BugSplat documentation [here](../../../../../), or email the team at [support@bugsplat.com](mailto:support@bugsplat.com).
{% endhint %}

## Overview

This document explains how your Microsoft Visual C++ application can be modified to provide full debug information to BugSplat website when it crashes.

### First steps

To begin [Download](https://www.bugsplat.com/docs/sdk/) and unzip the BugSplat software development kit for Microsoft Visual C++.

To get a feel for the BugSplat service before enabling your application, feel free to experiment with the [myConsoleCrasher sample application](https://www.bugsplat.com/docs/sdk/testapps/myconsolecrasher). You can find the Visual Studio project file located in your download folder at `...\BugSplatNative\BugSplat\samples\myConsoleCrasher\myConsoleCrasher.vcxproj`.

Run the sample application without the debugger attached to post a sample crash report to our [public database](https://www.bugsplat.com/docs/faq/public-database).

You may also want to browse the [BugSplat Native API](http://www.bugsplat.com/docs/BugSplatNative/html/index.html) documentation.

### Generating crash reports with your application

Add BugSplat to your application using the following steps:

* Include **`BugSplat.h`**.
* Create an instance of MiniDmpSender following the example in myConsoleCrasher. The MiniDmpSender constructor requires three parameters: BugSplat database, application name and version. The BugSplat database is created and selected on the Databases page. Typically, you will create a new database for each major release of your product. You supply application name and version to match your product release. These same values must be used when uploading symbol files for your application.
* Link your application to **`BugSplat.lib`**.
* Add **`BsSndRpt.exe`**, **`BugSplat.dll`**, and **`BugSplatRC.dll`** to your application's installer.

You should modify your build settings so that symbol files are created for release builds, e.g., 

![MyConsoleCrasher property pages image two for BugSplat crash reporting with C++](https://www.bugsplat.com/assets/img/docs/image-18.png)

![MyConsoleCrasher property pages image one for BugSplat crash reporting with C++](https://www.bugsplat.com/assets/img/docs/image-17.png)

**Note:** To get fully detailed call stacks and variable names for each crash on the BugSplat website, every time you build a release version of your application for distribution or internal testing, you should upload all **`.exe`**, **`.dll`**, and **`.pdb`** files for your product on the [Symbols Page](https://app.bugsplat.com/v2/symbols/). Better yet, use the [SendPdbs](https://www.bugsplat.com/docs/faq/sendpdbs) application as part of your build process to automate symbol upload to BugSplat.

Test your application by forcing a crash and verifying that the BugSplat dialog appears and that crashes are posted to your BugSplat account.

**Finally**, make sure to check that symbol names in the call stack are resolved correctly. If they aren’t, double-check that the correct version of symbol files and all executables for your application have been uploaded on the [Symbols](https://app.bugsplat.com/v2/symbols/) page.

## Toolset Considerations

MyConsoleCrasher targets the v120\_xp toolset which is not installed by later versions of Visual Studio. To build MyConsoleCrasher you may need to update the project's toolset and platform version.

The following steps are for Visual Studio 2017, other versions of Visual Studio may require slightly different steps.

To update your project's toolset and platform version, right click your project in Visual Studio, then choose Properties. In the properties menu, change "Platform Toolset" to a version available in your environment and click OK to save your changes.

![C++ Visual Studio Platform Toolset](https://www.bugsplat.com/assets/img/docs/cplusplus_platform_toolset.png)

Close the properties menu and right click the project again. This time choose the option to "Retarget Projects". Choose Windows SDK Version 10.\* and click OK. You should now be able to build MyConsoleCrasher.

![C++ Visual Studio Retarget Projects](https://www.bugsplat.com/assets/img/docs/cplusplus_retarget_projects.png)

## Further options

Instructions for modifying the default crash dialog can be found on the [Windows Dialog Box ](../../../../../education/how-tos/customize-the-crash-dialog.md)page.

## Dependencies

See technology dependencies at our [dependencies page](dependencies.md).

