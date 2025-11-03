# BugSplat for Windows (C++)

{% hint style="info" %}
Need help upgrading from an older version of BugSplat? Check out our [upgrade guide](bugsplat-for-windows-upgrade-guide.md) to get started.
{% endhint %}

### Overview 👀

This document explains how to modify your Microsoft Visual C++ application to provide full debug information to the BugSplat web application when it crashes.

### Getting Started 🚦&#x20;

To begin, [download](https://app.bugsplat.com/browse/download_item.php?item=native) and unzip the BugSplat SDK for Microsoft Visual C++.

To get a feel for the BugSplat service before enabling your application, feel free to experiment with the [MyConsoleCrasher sample application](../../../posting-a-test-crash/myconsolecrasher-c-plus-plus/), which is included as part of the software development kit.

### Integration 🏗️

{% hint style="warning" %}
For WinUI 3 applications, BugSplat must be registered as a WER [RuntimeExceptionModule](https://learn.microsoft.com/en-us/windows/win32/api/werapi/nf-werapi-werregisterruntimeexceptionmodule). This process is demonstrated in our sample WinUI project that we can send upon request. If you are interested, please contact [support@bugsplat.com](mailto:support@bugsplat.com).
{% endhint %}

Add BugSplat to your application using the following steps:

1. Link with **`BugSplat.lib`**  by adding an entry to `Linker > Input > Additional Dependencies`.
2. Add **`BugSplatMonitor.exe`**, **`BugSplatWer.dll`**, and **`BugSplatRc.dll`** to your application's installer.
3. Ensure your installer runs with Administrator privileges and creates a `RuntimeExceptionHelperModules` registry key with a name containing the full path to `BugSplatWer.dll`. For more information about configuring WER see this [doc](bugsplat-for-windows-upgrade-guide.md#registry-changes).

<figure><img src="../../../../../.gitbook/assets/image (85).png" alt=""><figcaption></figcaption></figure>

4. Include **`BugSplat.h`** in your application's source.
5. Create an instance of `BugSplat` following the example in [MyConsoleCrasher](../../../posting-a-test-crash/myconsolecrasher-c-plus-plus/). The BugSplat constructor requires three parameters: `database`, `application`, and `version`. A new BugSplat database can be created on the [Database](https://app.bugsplat.com/v2/database) page. Choose values for application name and version to match your product release. These same values are typically used when uploading symbol files for your application. Learn more about symbol uploads at [symbol-upload](../../../../../education/faq/how-to-upload-symbol-files-with-symbol-upload.md).

```cpp
#include "BugSplat.h"

BugSplat g_BugSplat(BUGSPLAT_DATABASE, APPLICATION_NAME, APPLICATION_VERSION);
```

6. Modify your build settings so that symbol files are created for release builds. Set `C/C++ > General > Debug Information Format` to `Program Database /Zi`. Be sure to also set `Linker > Debugging > Generate Debug Info` to `Yes (/DEBUG)`.
7. Configure a Post-Build step to upload your application's [symbol files](../../../../development/working-with-symbol-files/). Your script should authenticate using OAuth Client Credentials. You can create credentials on the [Integrations](https://app.bugsplat.com/v2/database/integrations#oauth) page under the OAuth tab.

```batch
symbol-upload-windows.exe -b your-database -a your-app -v your-version -i your-client-id -s your-client-secret -d "$(OutputDir)\"
```

{% hint style="info" %}
To get complete symbolicated call stacks and variable names for each crash, you should upload all **`.exe`**, **`.dll`**, and **`.pdb`** files for your product every time you build a release version of your application for distribution or internal testing.
{% endhint %}

### Verification ✅

Test your application by forcing a crash.

```cpp
*(volatile int *)0 = 0;
```

Verify that the BugSplat dialog appears, and that crashes are posted to your BugSplat account. Ensure that symbol names in the call stack are resolved correctly. If they aren’t, double-check that the correct version of symbol files and all executables for your application have been uploaded to BugSplat.

If everything was configured correctly, you should see a crash report that looks like this in your BugSplat database.

<figure><img src="../../../../../.gitbook/assets/image (86).png" alt="BugSplat Crash Page"><figcaption><p>BugSplat Crash Page</p></figcaption></figure>

#### Crash Dialog

Instructions for modifying the default crash dialog can be found on the [Windows Dialog Box](../../../../../education/how-tos/customize-the-crash-dialog.md) page.

## Dependencies

See technology dependencies on our [dependencies page](dependencies.md).
