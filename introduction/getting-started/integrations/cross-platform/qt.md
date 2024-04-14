# Qt

## Sample

BugSplat has developed a sample application demonstrating a cross-platform Qt crash reporting solution with Crashpad. The MyQtCrasher sample application provides a good starting point for developers wishing to capture Windows, macOS, and Linux crashes. Additionally, the sample provides symbol upload [scripts](https://github.com/BugSplat-Git/my-qt-crasher/tree/master/Crashpad/Tools) that you can incorporate into your build tools to generate crash reports with fully symbolicated call stacks

The MyQtCrasher sample application is available on [GitHub](https://github.com/BugSplat-Git/my-qt-crasher).

## Building Crashpad

BugSplat leverages [Crashpad](https://chromium.googlesource.com/crashpad/crashpad/+/master/README.md) to provide crash reporting for macOS, Windows, and Linux Qt applications. Please see this [article](crashpad/how-to-build-google-crashpad.md) for an in-depth guide that discusses how to build Crashpad.

For Windows, you'll need to build shared libraries for both Release (`/MD`) and Debug (`/MDd`) configurations. You'll also want to consider building with [Whole Program Optimization](https://docs.microsoft.com/en-us/cpp/build/reference/gl-whole-program-optimization?view=msvc-170) turned off (`/GL-`). To build shared libraries, generate your Crashpad build using the following terminal command:

```
gn gen out\MD --args="extra_cflags=\"/MD /GL-\"" && gn gen out\MDd --args="extra_cflags=\"/MDd /GL-\""
```

The snippet above works with Windows CMD, and depending on the terminal you're using, you might get various errors related to escape characters. If you choose to omit the `/GL-` flag you must ensure that you build Crashpad with the same version of MSVC you use for building your Qt application otherwise your project [will not build](https://stackoverflow.com/questions/62396117/integrating-crashpad-with-a-windows-qt-application). Setting the version of MSVC that builds Crashpad can be done by instead generating your configuration using the command `gn gen out/Default --winsdk="10.0.19041.0" --ide="vs2017"`.

For more info on how to build Crashpad shared libraries on Windows, see this [post](https://stackoverflow.com/questions/55302553/how-to-build-dynamic-shared-libraries-of-crashpad).

## Integrating Crashpad

Once Crashpad has been built, you'll need to add the relevant include directories to your project. Copy all of the Crashpad `.h` files to the directory `$$PWD/Crashpad/Include/crashpad` where `$$PWD` is your project's working directory. Add the include directories to your project by pasting the following snippet at the top of your project file:

```
# Include directories for Crashpad libraries
INCLUDEPATH += $$PWD/Crashpad/Include/crashpad
INCLUDEPATH += $$PWD/Crashpad/Include/crashpad/third_party/mini_chromium/mini_chromium
INCLUDEPATH += $$PWD/Crashpad/Include/crashpad/out/Default/gen
```

Add values corresponding to your BugSplat database, application name, and version. Note these values, as they will be used to configure Crashpad later in the configuration process.

```
# BugSplat configuration options
BUGSPLAT_DATABASE = fred            # Replace with your BugSplat database
BUGSPLAT_APPLICATION = myQtCrasher  # Replace with the name of your app
BUGSPLAT_VERSION = 1.1              # Replace with your apps version
BUGSPLAT_USER = fred@bugsplat.com   # Your BugSplat email (for uploading symbols)
BUGSPLAT_PASSWORD = Flintstone      # Your BugSplat password (for uploading symbols)
```

Next, link your app with the Crashpad libraries. Linking with the Crashpad libraries is platform-dependent.

### **macOS**

Copy `libcommon.a`, `libbase.a`, `libutil.a`, `libclient.a` , and `libmig_output.a` into `$$PWD/Crashpad/Libraries/MacOS`. You'll need to link to versions of these libraries that were built to target either `arm64` or `x86_64` depending on which architecture your build targets. You'll also need to link with the system libraries `libbsm`, `AppKit.Framework`, and `Security.Framework`. Add the following snippet to your project file to link with the aforementioned libraries:

```
# Crashpad rules for MacOS
macx {
    # Choose either x86_64 or arm64
    #ARCH = x86_64
    ARCH = arm64

    # Crashpad libraries
    LIBS += -L$$PWD/Crashpad/Libraries/MacOS/$$ARCH -lcommon
    LIBS += -L$$PWD/Crashpad/Libraries/MacOS/$$ARCH -lclient
    LIBS += -L$$PWD/Crashpad/Libraries/MacOS/$$ARCH -lbase
    LIBS += -L$$PWD/Crashpad/Libraries/MacOS/$$ARCH -lutil
    LIBS += -L$$PWD/Crashpad/Libraries/MacOS/$$ARCH -lmig_output

    # System libraries
    LIBS += -L/usr/lib/ -lbsm
    LIBS += -framework AppKit
    LIBS += -framework Security
}
```

You'll need to include a copy of the `crashpad_handler` executable with your application. Again, be sure to copy the version of `crashpad_handler` that targets either `arm64` or `x86_64` depending on what architecture you're targeting.

Copy `crashpad_handler` to the `$$PWD/Crashpad/Bin/MacOS` directory. Add the following snippet to the `macx` section of your project file that copies the macOS `crashpad_handler` to your project's build directory.

<pre><code><strong># Crashpad rules for MacOS
</strong>macx {
    ...
    # Copy crashpad_handler to build directory
    QMAKE_POST_LINK += "mkdir -p $$OUT_PWD/crashpad"
    QMAKE_POST_LINK += "&#x26;&#x26; cp $$PWD/Crashpad/Bin/MacOS/crashpad_handler $$OUT_PWD/crashpad"
}
</code></pre>

### **Windows**

Copy `base.lib`, `common.lib`, `client.lib` and `util.lib` into `$$PWD/Crashpad/Libraries/Windows`. You'll need to link with the system library `Advapi32`. Add the following snippet to your project file to link with the aforementioned libraries:

```
# Crashpad rules for Windows
win32 {
    # Crashpad libraries
    LIBS += -L$$PWD/Crashpad/Libraries/Windows/ -lbase
    LIBS += -L$$PWD/Crashpad/Libraries/Windows/ -lcommon
    LIBS += -L$$PWD/Crashpad/Libraries/Windows/ -lclient
    LIBS += -L$$PWD/Crashpad/Libraries/Windows/ -lutil

    # System libraries
    LIBS += -lAdvapi32
}
```

Additionally, you'll need to ship a copy of the `crashpad_handler.exe` executable with your application.

Copy `crashpad_handler.exe` to the `$$PWD/Crashpad/Bin/Windows` directory. Add the following snippet to the `win32` section of your project file that copies the Windows `crashpad_handler.exe` to your project's build directory.

```
# Crashpad rules for Windows
win32 {
    ...
    # Build variables
    CONFIG(debug, debug|release) {
        EXEDIR = $$OUT_PWD\debug
    }
    CONFIG(release, debug|release) {
        EXEDIR = $$OUT_PWD\release
    }

    # Copy crashpad_handler.exe to output directory
    QMAKE_POST_LINK += "copy /y $$shell_path($$PWD)\Crashpad\Bin\Windows\crashpad_handler.exe $$shell_path($$OUT_PWD)\crashpad"
}
```

### **Linux**

Copy `libbase.a`, `libutil.a`, `libcommon.a` and `libclient.a` into `$$PWD/Crashpad/Libraries/Linux`. The order in which you specify the Crashpad libraries to link is important! `libcommon.a`, and `libclient.a` must be specified first, then `libutil.a` and finally `libbase.a`. Add the following snippet to your project file to link with the aforementioned libraries:

```
# Crashpad rules for Linux
linux {
    # Crashpad libraries
    LIBS += -L$$PWD/Crashpad/Libraries/Linux/ -lcommon
    LIBS += -L$$PWD/Crashpad/Libraries/Linux/ -lclient
    LIBS += -L$$PWD/Crashpad/Libraries/Linux/ -lutil
    LIBS += -L$$PWD/Crashpad/Libraries/Linux/ -lbase
}
```

Additionally, you'll need to ship a copy of the `crashpad_handler` executable with your application. Copy `crashpad_handler` to the `$$PWD/Crashpad/Bin/Linux` directory. Add the following snippet to the `linux` section of your project file that copies the Linux `crashpad_handler` to your project's build directory.

```
# Crashpad rules for Linux
linux {
    ...
    # Copy crashpad_handler to build directory
    QMAKE_POST_LINK += "cp $$PWD/Crashpad/Bin/Linux/crashpad_handler $$OUT_PWD/crashpad"
}
```

## Configuring Crashpad

To enable Crashpad in your application, you must configure the Crashpad handler with your BugSplat database, application name, and application version. The following is a macOS, Windows, and Linux-compatible snippet that will configure the Crashpad handler:

```cpp
#include <QApplication>
#include <vector>
#include "paths.h"
#include "client/crash_report_database.h"
#include "client/crashpad_client.h"
#include "client/settings.h"

using namespace base;
using namespace crashpad;

bool initializeCrashpad(QString dbName, QString appName, QString appVersion);
QString getExecutableDir(void);

bool initializeCrashpad(QString dbName, QString appName, QString appVersion)
{
    // Get directory where the exe lives so we can pass a full path to handler, reportsDir and metricsDir
    QString exeDir = getExecutableDir();

    // Helper class for cross-platform file systems
    Paths crashpadPaths(exeDir);

    // Ensure that crashpad_handler is shipped with your application
    FilePath handler(Paths::getPlatformString(crashpadPaths.getHandlerPath()));

    // Directory where reports will be saved. Important! Must be writable or crashpad_handler will crash.
    FilePath reportsDir(Paths::getPlatformString(crashpadPaths.getReportsPath()));

    // Directory where metrics will be saved. Important! Must be writable or crashpad_handler will crash.
    FilePath metricsDir(Paths::getPlatformString(crashpadPaths.getMetricsPath()));

    // Configure url with your BugSplat database
    QString url = "https://" + dbName + ".bugsplat.com/post/bp/crash/crashpad.php";

    // Metadata that will be posted to BugSplat
    QMap<std::string, std::string> annotations;
    annotations["format"] = "minidump";                 // Required: Crashpad setting to save crash as a minidump
    annotations["database"] = dbName.toStdString();     // Required: BugSplat database
    annotations["product"] = appName.toStdString();     // Required: BugSplat appName
    annotations["version"] = appVersion.toStdString();  // Required: BugSplat appVersion
    annotations["key"] = "Sample key";                  // Optional: BugSplat key field
    annotations["user"] = "fred@bugsplat.com";          // Optional: BugSplat user email
    annotations["list_annotations"] = "Sample comment";	// Optional: BugSplat crash description

    // Disable crashpad rate limiting so that all crashes have dmp files
    std::vector<std::string> arguments;
    arguments.push_back("--no-rate-limit");

    // Initialize crashpad database
    std::unique_ptr<CrashReportDatabase> database = CrashReportDatabase::Initialize(reportsDir);
    if (database == NULL) return false;

    // Enable automated crash uploads
    Settings *settings = database->GetSettings();
    if (settings == NULL) return false;
    settings->SetUploadsEnabled(true);

    // Attachments to be uploaded alongside the crash - default bundle size limit is 20MB
    std::vector<FilePath> attachments;
    FilePath attachment(Paths::getPlatformString(crashpadPaths.getAttachmentPath()));
#if defined(Q_OS_WINDOWS) || defined(Q_OS_LINUX)
    // Crashpad hasn't implemented attachments on OS X yet
    attachments.push_back(attachment);
#endif

    // Start crash handler
    CrashpadClient *client = new CrashpadClient();
    bool status = client->StartHandler(handler, reportsDir, metricsDir, url.toStdString(), annotations.toStdMap(), arguments, true, true, attachments);
    return status;
}
```

Be sure to update the values for `dbName`, `appName` and `appVersion` to values specific to your application. The `Paths` class allows you to get platform-specific paths for Crashpad; its source can be found [here](https://github.com/BugSplat-Git/myQtCrasher/blob/master/paths.cpp). To configure the paths to `crashpad_handler`, `metricsDir`, `reportsDir` and `attachment` you'll first want to find the location of your executable using the sample code below:

```cpp
#if defined(Q_OS_WIN)
    #define NOMINMAX
    #include <windows.h>
#endif

#if defined(Q_OS_MAC)
    #include <mach-o/dyld.h>
#endif

#if defined(Q_OS_LINUX)
    #include <unistd.h>
    #define MIN(x, y) (((x) < (y)) ? (x) : (y))
#endif

QString getExecutableDir() {
#if defined(Q_OS_MAC)
    unsigned int bufferSize = 512;
    std::vector<char> buffer(bufferSize + 1);

    if(_NSGetExecutablePath(&buffer[0], &bufferSize))
    {
        buffer.resize(bufferSize);
        _NSGetExecutablePath(&buffer[0], &bufferSize);
    }

    char* lastForwardSlash = strrchr(&buffer[0], '/');
    if (lastForwardSlash == NULL) return NULL;
    *lastForwardSlash = 0;

    return &buffer[0];
#elif defined(Q_OS_WINDOWS)
    HMODULE hModule = GetModuleHandleW(NULL);
    WCHAR path[MAX_PATH];
    DWORD retVal = GetModuleFileNameW(hModule, path, MAX_PATH);
    if (retVal == 0) return NULL;

    wchar_t *lastBackslash = wcsrchr(path, '\\');
    if (lastBackslash == NULL) return NULL;
    *lastBackslash = 0;

    return QString::fromWCharArray(path);
#elif defined(Q_OS_LINUX)
    char pBuf[FILENAME_MAX];
    int len = sizeof(pBuf);
    int bytes = MIN(readlink("/proc/self/exe", pBuf, len), len - 1);
    if (bytes >= 0) {
        pBuf[bytes] = '\0';
    }

    char* lastForwardSlash = strrchr(&pBuf[0], '/');
    if (lastForwardSlash == NULL) return NULL;
    *lastForwardSlash = '\0';

    return QString::fromStdString(pBuf);
#else
    #error getExecutableDir not implemented on this platform
#endif
}
```

Add a call to `initializeCrashpad` at your application's entry point.

```cpp
int main(int argc, char *argv[]) {
    QString dbName = "Fred";
    QString appName = "myQtCrasher";
    QString appVersion = "1.0";
    initializeCrashpad(dbName, appName, appVersion);
}
```

### Symbols

{% hint style="info" %}
You can download the platform-specific symbol-upload executables on the [GitHub releases page](https://github.com/BugSplat-Git/symbol-upload/releases).&#x20;
{% endhint %}

To get function names and line numbers in your crash reports, you will need to generate and upload `.sym` files to BugSplat. Crashpad `.sym` files can be generated from a macOS `.dSYM` file, a Windows `.pdb` file or a Linux `.debug` file.

To generate `.dSYM`, `.pdb` and `.debug` files add the following to the project file:

```
# Create symbols for dump_syms and symupload
CONFIG += force_debug_info
CONFIG += separate_debug_info
```

BugSplat's symbol-upload executable can generate `.sym` files as part of the symbol upload process. To generate `.sym` files from your `.dSYM`, `.pdb`, and `.debug` files, invoke symbol-upload with the `-m` flag as demonstrated in the platform-specific examples below.

### **macOS**

{% hint style="warning" %}
If you downloaded symbol-upload-macos via a web browser, you will need to [right-click and open](https://www.idownloadblog.com/2017/04/20/fix-application-from-internet-gatekeeper/#1-Right-click-to-open) the file before you can run the `symbol-upload command`.
{% endhint %}

To generate and upload `.sym` files as part of your build, create a `symbols.sh` script that calls `symbol-upload-macos`:

```bash
symbol_upload="${1}/Crashpad/Tools/MacOS/symbol-upload-macos"
database="${3}"
app="${4}"
version="${5}"
dir="${2}"
file="${4}.app.dSYM"
user="${6}"
password="${7}"

eval "${symbol_upload} -b ${database} -a \"${app}\" -v \"${version}\" -d \"${dir}\" -f \"${file}\" -u \"${user}\" -p \"${password}\" -m"
```

Call `symbols.sh` from your `QMAKE_POST_LINK` step:

```
# Crashpad rules for MacOS
macx {
    ...
    # Copy crashpad_handler, attachment.txt to build directory, and upload symbols
    QMAKE_POST_LINK += "mkdir -p $$OUT_PWD/crashpad"
    QMAKE_POST_LINK += "&& cp $$PWD/Crashpad/Bin/MacOS/crashpad_handler $$OUT_PWD/crashpad"
    QMAKE_POST_LINK += "&& bash $$PWD/Crashpad/Tools/MacOS/symbols.sh $$PWD $$OUT_PWD $$BUGSPLAT_DATABASE $$BUGSPLAT_APPLICATION $$BUGSPLAT_VERSION $$BUGSPLAT_USER $$BUGSPLAT_PASSWORD"
}
```

After each build, you must re-upload symbol files. For best results, you should increment the version number each time you release your application. The `dbName`, `appName,` and `appVersion` values from the [Configuring Crashpad](qt.md#configuring-crashpad) section must match the values set for `$$BUGSPLAT_DATABASE`, `$$BUGSPLAT_APPLICATION` , and `$$BUGSPLAT_VERSION`.

### **Windows**

To generate and upload `.sym` files as part of your build, create a `symbols.sh` script that calls `symbol-upload-windows.exe`:

```batch
%1\Crashpad\Tools\Windows\symbol-upload-windows.exe -b %3 -a "%4" -v "%5" -d "%2" -f "%4.exe" -u "%6" -p "%7" -m
```

Call `symbols.bat` from your `QMAKE_POST_LINK` step:

```bash
# Crashpad rules for Windows
win32 {
    ...
    # Copy crashpad_handler, attachment.txt to build directory, and upload symbols
    QMAKE_POST_LINK += "mkdir $$shell_path($$OUT_PWD)\crashpad"
    QMAKE_POST_LINK += "& copy /y $$shell_path($$PWD)\Crashpad\Bin\Windows\crashpad_handler.exe $$shell_path($$OUT_PWD)\crashpad\crashpad_handler.exe"
    QMAKE_POST_LINK += "&& $$shell_path($$PWD)\Crashpad\Tools\Windows\symbols.bat $$shell_path($$PWD) $$shell_path($$EXEDIR) $$BUGSPLAT_DATABASE $$BUGSPLAT_APPLICATION $$BUGSPLAT_VERSION $$BUGSPLAT_USER $$BUGSPLAT_PASSWORD"
    QMAKE_POST_LINK += "&& copy /y $$shell_path($$PWD)\Crashpad\attachment.txt $$shell_path($$OUT_PWD)\attachment.txt"
}
```

After each build, you must re-upload symbol files. For best results, you should increment the version number each time you release your application. The `dbName`, `appName,` and `appVersion` values from the [Configuring Crashpad](qt.md#configuring-crashpad) section must match the values set for `$$BUGSPLAT_DATABASE`, `$$BUGSPLAT_APPLICATION` , and `$$BUGSPLAT_VERSION`.

### **Linux**

To generate and upload `.sym` files as part of your build, create a `symbols.sh` script that calls `symbol-upload-macos`:

```bash
#!/bin/bash
symbol_upload="${1}/Crashpad/Tools/Linux/symbol-upload-linux"
database="${3}"
app="${4}"
version="${5}"
dir="${2}"
file="${4}.debug"
user="${6}"
password="${7}"

eval "${symbol_upload} -b ${database} -a \"${app}\" -v \"${version}\" -d \"${dir}\" -f \"${file}\" -u \"${user}\" -p \"${password}\" -m"
```

Call `symbols.sh` from your `QMAKE_POST_LINK` step:

<pre><code># Crashpad rules for Linux
linux {
<strong>    ...
</strong>    # Copy crashpad_handler, attachment.txt to build directory, and upload symbols
    QMAKE_POST_LINK += "mkdir -p $$OUT_PWD/crashpad &#x26;&#x26; cp $$PWD/Crashpad/Bin/Linux/crashpad_handler $$OUT_PWD/crashpad/crashpad_handler"
    QMAKE_POST_LINK += "&#x26;&#x26; $$PWD/Crashpad/Tools/Linux/symbols.sh $$PWD $$OUT_PWD $$BUGSPLAT_DATABASE $$BUGSPLAT_APPLICATION $$BUGSPLAT_VERSION $$BUGSPLAT_USER $$BUGSPLAT_PASSWORD"
    QMAKE_POST_LINK += "&#x26;&#x26; cp $$PWD/Crashpad/attachment.txt $$OUT_PWD/attachment.txt"
}
</code></pre>

After each build, you must re-upload symbol files. For best results, you should increment the version number each time you release your application. The `dbName`, `appName,` and `appVersion` values from the [Configuring Crashpad](qt.md#configuring-crashpad) section must match the values set for `$$BUGSPLAT_DATABASE`, `$$BUGSPLAT_APPLICATION` , and `$$BUGSPLAT_VERSION`.

## Generating a Crash Report

Force a crash in your application after Crashpad has been initialized:

```cpp
*(volatile int *)0 = 0;
```

After you've submitted a crash report, navigate to the [Dashboard](https://app.bugsplat.com/v2/dashboard) page. Click the link in the `ID` column to see the details of your crash report. The following image is from our sample `myQtCrasher` application:

![BugSplat Qt Crash](<../../../../.gitbook/assets/image (1) (3) (1).png>)
