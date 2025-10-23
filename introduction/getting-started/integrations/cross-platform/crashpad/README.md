# Crashpad

## Overview ℹ️

{% hint style="warning" %}
**Want help building Crashpad?** View our [step-by-step guide](how-to-build-google-crashpad.md) to help you get started more quickly.
{% endhint %}

Crashpad is the latest open-source crash reporting tool built by Google and is the successor to the popular Breakpad crash reporter. Crashpad allows you to submit minidumps to a configured URL after a crash occurs in your product. Crashpad and Breakpad are 'wire compatible.' The crash reports created by both systems are processed similarly on our backend.

The official Crashpad documentation is available [here](https://chromium.googlesource.com/crashpad/crashpad/+/master/README.md).&#x20;

## Build Configuration 🏗️

The first step to integrating Crashpad is to configure your build process. BugSplat provides pre-built versions of Crashpad that are updated monthly. You can find the prebuilt libraries on the [Releases](https://github.com/BugSplat-Git/bugsplat-crashpad/releases) page in the [bugsplat-crashpad](https://github.com/BugSplat-Git/bugsplat-crashpad/releases) repo. The [bugsplat-crashpad](https://github.com/BugSplat-Git/bugsplat-crashpad) repo also includes scripts that demonstrate how to build Crashpad on [Windows](https://github.com/BugSplat-Git/bugsplat-crashpad/blob/main/scripts/build_crashpad_windows_msvc.ps1), [macOS](https://github.com/BugSplat-Git/bugsplat-crashpad/blob/main/scripts/build_crashpad_macos.sh), and [Linux](https://github.com/BugSplat-Git/bugsplat-crashpad/blob/main/scripts/build_crashpad_linux.sh) with Chromium's [Depot Tools](https://www.chromium.org/developers/how-tos/depottools/).

We also offer a detailed walkthrough that describes how to [build Crashpad from scratch](how-to-build-google-crashpad.md).

### Binaries and Libraries

To add Crashpad to your application, you'll need to link with the `client` , `common`, `util`, and `base` libraries. Additionally, on macOS, you'll need to link with the `mig_output` library. On all platforms, you'll need to copy `crashpad_handler` to your build output directory and include it in your installer so that it is available at runtime.

The following example is a snippet from [CMakeLists.txt](https://github.com/BugSplat-Git/bugsplat-crashpad/blob/main/CMakeLists.txt) for Windows - please see our [sample](https://github.com/BugSplat-Git/bugsplat-crashpad/blob/main/CMakeLists.txt) for macOS and Linux.

<pre class="language-cmake"><code class="lang-cmake"># Library vars
set(CRASHPAD_LIBRARIES
    ${CRASHPAD_OUT_DIR}/obj/client/client.lib
<strong>    ${CRASHPAD_OUT_DIR}/obj/client/common.lib
</strong>    ${CRASHPAD_OUT_DIR}/obj/util/util.lib
    ${CRASHPAD_OUT_DIR}/obj/third_party/mini_chromium/mini_chromium/base/base.lib
)

# Handler var
set(CRASHPAD_HANDLER ${CRASHPAD_OUT_DIR}/crashpad_handler.exe)

# Link crashpad libs
target_link_libraries(MyCMakeCrasher ${CRASHPAD_LIBRARIES})

# Copy handler exe
add_custom_command(TARGET MyCMakeCrasher POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy
    ${CRASHPAD_HANDLER}
    ${CMAKE_CURRENT_BINARY_DIR}/${OUTPUT_CONFIG_DIR}/crashpad_handler${CMAKE_EXECUTABLE_SUFFIX}
)
</code></pre>

### Include Directories

You'll also need to add a few Crashpad directories to your app's includes list.

```cmake
# Include dirs
include_directories(
    ${CRASHPAD_ROOT}
    ${CRASHPAD_ROOT}/third_party/mini_chromium/mini_chromium
)
```

## Code Integration 🧑‍💻

The [bugsplat-crashpad](https://github.com/BugSplat-Git/bugsplat-crashpad) repo provides a [my-cmake-crasher](https://github.com/bobbyg603/bugsplat-crashpad/blob/main/main.cpp) sample application that demonstrates how to configure Crashpad. The following code snippets are from the [my-cmake-crasher example](https://github.com/bobbyg603/bugsplat-crashpad/blob/main/main.cpp).

### Includes and Namespaces

Add the following:

```cpp
#include "client/crashpad_client.h"
#include "client/crash_report_database.h"
#include "client/settings.h"

using namespace crashpad;
using namespace std;
```

### Initialization

Copy the `initializeCrashpad` and `getExecutableDir` methods from the BugSplat sample. The Crashpad `url` and parameters `format`, `database`, `product` and `version` are required to upload crash reports to BugSplat. You can optionally specify values for the parameters `user`, `list_annotations`, and `key` which will be tracked with each crash report.

The following snippet is for Windows. Please see [my-cmake-crasher](https://github.com/bobbyg603/bugsplat-crashpad/blob/main/main.cpp) for macOS, and Linux compatible code changes.

```cpp
// Function to initialize Crashpad with BugSplat integration
bool initializeCrashpad(std::string dbName, std::string appName, std::string appVersion) {
    using namespace crashpad;
    
    // Get the directory where the exe lives
    std::string exeDir = getExecutableDir();

    // Ensure that crashpad_handler is shipped with your application
    base::FilePath handler(base::FilePath::StringType(exeDir.begin(), exeDir.end()) + L"/crashpad_handler.exe");

    // Directory where reports and metrics will be saved
    base::FilePath reportsDir(base::FilePath::StringType(exeDir.begin(), exeDir.end()));
    base::FilePath metricsDir(base::FilePath::StringType(exeDir.begin(), exeDir.end()));

    // Configure URL with your BugSplat database
    std::string url = "https://" + dbName + ".bugsplat.com/post/bp/crash/crashpad.php";

    // Metadata that will be posted to BugSplat
    std::map<std::string, std::string> annotations;
    annotations["format"] = "minidump";              // Required: Crashpad setting to save crash as a minidump
    annotations["database"] = dbName;                // Required: BugSplat database
    annotations["product"] = appName;                // Required: BugSplat appName
    annotations["version"] = appVersion;             // Required: BugSplat appVersion
    annotations["key"] = "Sample key";               // Optional: BugSplat key field
    annotations["user"] = "fred@bugsplat.com";       // Optional: BugSplat user email
    annotations["list_annotations"] = "Sample crash from dynamic library"; // Optional: BugSplat crash description

    // Disable crashpad rate limiting
    std::vector<std::string> arguments;
    arguments.push_back("--no-rate-limit");

    // File paths of attachments to be uploaded with the minidump file at crash time
    std::vector<base::FilePath> attachments;
    base::FilePath attachment(base::FilePath::StringType(exeDir.begin(), exeDir.end()) + L"/attachment.txt");
    attachments.push_back(attachment);

    // Initialize Crashpad database
    std::unique_ptr<CrashReportDatabase> database = CrashReportDatabase::Initialize(reportsDir);
    if (database == nullptr) {
        return false;
    }

    // Enable automated crash uploads
    Settings* settings = database->GetSettings();
    if (settings == nullptr) {
        return false;
    }
    settings->SetUploadsEnabled(true);

    // Start crash handler
    CrashpadClient client;
    bool success = client.StartHandler(
        handler,
        reportsDir,
        metricsDir,
        url,
        annotations,
        arguments,
        true,  // Restartable
        true,  // Asynchronous
        attachments  // Add attachment
    );

    return success;
}

// Function to get the executable directory
std::string getExecutableDir() {
    char path[MAX_PATH];
    GetModuleFileNameA(NULL, path, MAX_PATH);
    std::string pathStr(path);
    size_t lastBackslash = pathStr.find_last_of('\\');
    if (lastBackslash != std::string::npos) {
        return pathStr.substr(0, lastBackslash);
    }
    return "";
}
```

Call `initializeCrashpad` using parameters specific to your application for `dbName`, `appName`, and `appVersion`.

```cpp
#define BUGSPLAT_DATABASE "your-database-here"
#define BUGSPLAT_APP_NAME "your-application-here"
#define BUGSPLAT_APP_VERSION "1.2.3-etc"

initializeCrashpad(BUGSPLAT_DATABASE, BUGSPLAT_APP_NAME, BUGSPLAT_APP_VERSION);
```

## Symbol Uploads 🗺️

You will need to generate and upload `.sym` files to BugSplat to generate function names and line numbers in the stack traces of your crash reports. BugSplat's [symbol-upload](https://github.com/BugSplat-Git/symbol-upload) tool can be used conveniently to generate and upload `.sym` files with a single terminal command. Download a copy of symbol-upload from [GitHub](https://github.com/BugSplat-Git/symbol-upload) or install it via [npm](https://npmjs.com/package/@bugsplat/symbol-upload).

Once you have symbol-upload downloaded or installed, modify the following command to suit your needs.

```bash
symbol-upload-windows -b "your-database-here" ^
    -a "your-application-here" ^
    -v "1.2.3-etc" ^
    -u "you@company.com" ^
    -p "*******" ^
    -d "C:\\path\\to\\build\\output" ^
    -f "*.{pdb,exe,dll}" ^
    --dumpSyms
```

## Testing 🧪

Trigger a crash in your application. The crash report should be available immediately on the BugSplat website. Click on the link in the `ID` column. If everything worked correctly, you should see something that resembles the following.

<figure><img src="../../../../../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption><p>BugSplat Crash Details Page</p></figcaption></figure>

## Additional Considerations 🤔

### Databases

The BugSplat database for your crash reports is created on the [Manage Database](https://app.bugsplat.com/v2/company/databases) page in Settings. Typically, you will create a new database for each major release of your product.

### Optimizations

Compiler optimizations can cause a mismatch between the line numbers in crash reports and the actual line numbers in your code. BugSplat recommends turning off compiler optimizations to ensure that the line numbers in your crash reports match the line numbers in your code. To turn off optimizations in Visual Studio, right-click your project and navigate to **Properties > C/C++ > Optimization > Optimization,** and set the value to **Disabled (/Od)**.
