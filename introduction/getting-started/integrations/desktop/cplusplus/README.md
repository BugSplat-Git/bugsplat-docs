# BugSplat Crash Reporting Library for Windows (Native C++)

## Overview

This document explains how your Microsoft Visual C++ application can be modified to provide full debug information to the BugSplat web application when it crashes. &#x20;

If you've used an older version of this library, our [porting guide](bugsplat-native-upgrade-guide.md) might be useful. &#x20;

### First steps

To begin [Download](https://app.bugsplat.com/browse/download_item.php?item=native) and unzip the BugSplat software development kit for Microsoft Visual C++.

To get a feel for the BugSplat service before enabling your application, feel free to experiment with the [myConsoleCrasher sample application](../../../posting-a-test-crash/myconsolecrasher-c-plus-plus/), which is included as part of the software development kit.

### Generating crash reports with your application

Add BugSplat to your application using the following steps:

* Include **`BugSplat.h`**.
* Create an instance of BugSplat following the example in myConsoleCrasher. The BugSplat constructor requires three parameters: BugSplat database, application name and version. The BugSplat database is created and selected on the Databases page. Typically, you will create a new database for each major release of your product. You supply the application name and version to match your product release. These same values are typically used when uploading symbol files for your application.  Learn more about symbol uploads at [symbol-upload](../../../../../education/faq/how-to-upload-symbol-files-with-symbol-upload.md).
* Link your application to **`BugSplat.lib`**.
* Add **`BugSplatMonitor.exe`**, **`BugSplatWer.dll`**, and **`BugSplatRC.dll`** to your application's installer.

Note that WinUI applications require special setup.  We have a sample WinUI project that we can send upon request.  If interested, please get in touch with support@bugsplat.com.

You should modify your build settings so that symbol files are created for release builds, e.g.,

![Microsoft Visual Studio C++ General Properties](../../../../../.gitbook/assets/microsoft-cpp-configuration-general.png)

![Microsoft Visual Studio C++ Debugging Properties](../../../../../.gitbook/assets/microsoft-cpp-configuration-debugging.png)

**Note:** To get complete symbolicated call stacks and variable names for each crash, every time you build a release version of your application for distribution or internal testing, you should upload all **`.exe`**, **`.dll`**, and **`.pdb`** files for your product.  Use the [symbol-upload](../../../../../education/faq/how-to-upload-symbol-files-with-symbol-upload.md) application as part of your build process to automate symbol upload to BugSplat.

Test your application by forcing a crash, verifying that the BugSplat dialog appears, and that crashes are posted to your BugSplat account.

**Finally**, make sure to check that symbol names in the call stack are resolved correctly. If they aren’t, double-check that the correct version of symbol files and all executables for your application have been uploaded to BugSplat.

#### Crash Dialog

Instructions for modifying the default crash dialog can be found on the [Windows Dialog Box ](../../../../../education/how-tos/customize-the-crash-dialog.md)page.

## Dependencies

See technology dependencies at our [dependencies page](dependencies.md).
