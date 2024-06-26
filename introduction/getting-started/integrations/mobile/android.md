# Android

## Introduction

The [Android NDK](https://developer.android.com/ndk) is a set of tools for building native C++ applications for Android. BugSplat recommends using [Crashpad](https://chromium.googlesource.com/crashpad/crashpad/+/master/README.md) to handle native crashes in Android NDK applications. Before integrating your application with BugSplat, make sure to review the [Getting Started](../../) resources and complete the simple startup tasks listed below.

BugSplat also provides a reference Android Studio project [my-android-crasher](https://github.com/BugSplat-Git/my-android-crasher) that includes [pre-built Crashpad libraries](https://github.com/BugSplat-Git/my-android-crasher/tree/master/app/src/main/cpp/crashpad/lib), shows how to [initialize Crashpad](https://github.com/BugSplat-Git/my-android-crasher/blob/e02e0b7afbf85912bdf03a25ea652daebde15e72/app/src/main/cpp/native-lib.cpp#L18-L69), demonstrates how to link with the Crashpad libraries and and copy the necessary files to your APK via [CMakeLists.txt](https://github.com/BugSplat-Git/my-android-crasher/blob/master/app/src/main/cpp/CMakeLists.txt).

## Building Crashpad

{% hint style="info" %}
For an in depth guide that discusses how to build Crashpad, please see this [article](../cross-platform/crashpad/how-to-build-google-crashpad.md).
{% endhint %}

To build Crashpad you'll first need to download a copy of the Chromium [depot\_tools](https://dev.chromium.org/developers/how-tos/install-depot-tools). Once you have downloaded `depot_tools`, you'll need to add the parent folder to your system's `PATH` environment variable. After adding `depot_tools` to your systems `PATH`, run the following commands to download the Crashpad repository:

```bash
mkdir ~/crashpad
cd ~/crashpad
fetch crashpad
cd crashpad
```

Next you'll need to generate Crashpad build configurations for each [Android ABI](https://chromium.googlesource.com/chromium/src/+/HEAD/docs/android\_build\_instructions.md#figuring-out-target\_cpu) your application supports. Generate build configurations for the `target_cpu` values `arm`, `arm64`, `x86`, and `x64`:

```bash
gn gen out/arm64-v8a --args='target_os="android" target_cpu="arm64" android_ndk_root="[Path to Android SDK]/ndk/21.4.7075529" android_api_level=21'
```

Build each of build configurations:

```
ninja -C out/arm64-v8a
```

## Integrating Crashpad

Once Crashpad has been built you'll need to add the relevant include directories to your project. Copy all of the Crashpad `.h` files to the directory `app/src/main/cpp/crashpad/include`. Next, add the include directories your project's `CMakeLists.txt` file:

```
# Crashpad Headers
include_directories(${PROJECT_SOURCE_DIR}/crashpad/include/ ${PROJECT_SOURCE_DIR}/crashpad/include/third_party/mini_chromium/mini_chromium/)
```

After adding the include directories, you'll need to add the Crashpad static libraries to your project. You'll need to add a set of Crashpad libraries for each ABI your application supports. From the `/crashpad/out/Debug/{{ABI}}` directory you'll want to copy the `client`, `util` and `third_party/mini_chromium/mini_chromium/base` folders to `app/src/main/cpp/crashpad/lib/{{ABI}}`.

![BugSplat Android Libs Crashpad Folders](../../../../.gitbook/assets/android-crashpad-libs.png)

Once all of the Crashpad libraries have been copied to your project directory, add the following to your project's `CMakeLists.txt` file to link the Crashpad libraries:

```
# Crashpad Libraries
add_library(crashpad_client STATIC IMPORTED)
set_property(TARGET crashpad_client PROPERTY IMPORTED_LOCATION ${PROJECT_SOURCE_DIR}/crashpad/lib/${ANDROID_ABI}/client/libcrashpad_client.a)

add_library(crashpad_util STATIC IMPORTED)
set_property(TARGET crashpad_util PROPERTY IMPORTED_LOCATION ${PROJECT_SOURCE_DIR}/crashpad/lib/${ANDROID_ABI}/util/libcrashpad_util.a)

add_library(base STATIC IMPORTED)
set_property(TARGET base PROPERTY IMPORTED_LOCATION ${PROJECT_SOURCE_DIR}/crashpad/lib/${ANDROID_ABI}/base/libbase.a)

# Specifies the target library
target_link_libraries(  
    native-lib
    crashpad_client
    crashpad_util
    base
)
```

Additionally, you'll need ship a copy of the `crashpad_handler` executable with your application. To do this, you'll need to rename `crashpad_handler` to `libcrashpad_handler.so` otherwise it will be ignored by the APK bundler. Copy `libcrashpad_handler.so` to `app/src/main/cpp/crashpad/lib/{{ABI}}` for each Crashpad ABI architecture. Add the following snippet to `CMakeLists.txt` so that `libcrashpad_handler.so` is copied to your device and made available at runtime:

```
# Crashpad Handler
add_library(crashpad_handler SHARED IMPORTED)
set_property(TARGET crashpad_handler PROPERTY IMPORTED_LOCATION ${PROJECT_SOURCE_DIR}/crashpad/lib/${ANDROID_ABI}/libcrashpad_handler.so)
```

## Configuring Crashpad

To enable Crashpad in your application you'll need to configure the Crashpad handler with your BugSplat database, application name and application version. The following snippet will configure the Crashpad handler:

```cpp
#include <jni.h>
#include <string>
#include <unistd.h>
#include "client/crashpad_client.h"
#include "client/crash_report_database.h"
#include "client/settings.h"

using namespace base;
using namespace crashpad;
using namespace std;

extern "C" JNIEXPORT jboolean JNICALL
Java_com_example_androidcrasher_MainActivity_initializeCrashpad(
    JNIEnv* env,
    jobject /* this */
) {

    string dataDir = "/data/data/com.example.androidcrasher";

    // Crashpad file paths
    FilePath handler(dataDir + "/lib/libcrashpad_handler.so");
    FilePath reportsDir(dataDir + "/crashpad");
    FilePath metricsDir(dataDir + "/crashpad");

    // Crashpad upload URL for BugSplat database
    string url = "http://{{database}}.bugsplat.com/post/bp/crash/crashpad.php";

    // Crashpad annotations
    map<string, string> annotations;
    annotations["format"] = "minidump";           // Required: Crashpad setting to save crash as a minidump
    annotations["database"] = "{{database}}";     // Required: BugSplat database
    annotations["product"] = "{{appName}}";       // Required: BugSplat appName
    annotations["version"] = "{{appVersion}}";    // Required: BugSplat appVersion
    annotations["key"] = "Key";                   // Optional: BugSplat key field
    annotations["user"] = "fred@bugsplat.com";    // Optional: BugSplat user email
    annotations["list_annotations"] = "Sample comment"; // Optional: BugSplat crash description

    // Crashpad arguments
    vector<string> arguments;
    arguments.push_back("--no-rate-limit");

    // Crashpad local database
    unique_ptr<CrashReportDatabase> crashReportDatabase = CrashReportDatabase::Initialize(reportsDir);
    if (crashReportDatabase == NULL) return false;

    // Enable automated crash uploads
    Settings *settings = crashReportDatabase->GetSettings();
    if (settings == NULL) return false;
    settings->SetUploadsEnabled(true);

    // File paths of attachments to be uploaded with the minidump file at crash time - default bundle limit is 20MB
    vector<FilePath> attachments;
    FilePath attachment(dataDir + "/files/attachment.txt");
    attachments.push_back(attachment);

    // Start Crashpad crash handler
    static CrashpadClient *client = new CrashpadClient();
    bool status = client->StartHandlerAtCrash(handler, reportsDir, metricsDir, url, annotations, arguments, attachments);
    return status;
}
```

Be sure to update the values for `database`, `appName`, `appVersion` and `packageName`to values specific to your application. Next add a call to `initializeCrashpad` at the entry point of your application. The following example defines `initializeCrashpad` as a native C++ function and calls it using Kotlin from MainActivity:

```kotlin
// MainActivity entry point
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)

    // Example of a call to a native method
    sample_text.text = if (initializeCrashpad()) "initialized" else "fail"
}

// Declare native C++ method
external fun initializeCrashpad(): Boolean
```

To test the attachments feature, use Java or Kotlin to write a text file. The following is a Kotlin snippet that creates a file `attachment.txt`:

```kotlin
// MainActivity entry point
override fun onCreate(savedInstanceState: Bundle?) {
    writeLogFile()
    ...
}

private fun writeLogFile() {
    try {
        val outputStreamWriter = OutputStreamWriter(
        applicationContext.openFileOutput(
                "attachment.txt",
                Context.MODE_PRIVATE
            )
        )
        outputStreamWriter.write("BugSplat rocks!")
        outputStreamWriter.close()
    } catch (e: IOException) {
        Log.e("Exception", "File write failed: " + e.toString())
    }
}
```

### Symbols

To ensure that your crash reports contain function names and line numbers you'll need to add an option to your build configuration that prevents symbolic information from being stripped. To prevent symbols from being stripped, add the following to your `build.gradle` file:

```
android {
  ...
  // Add this directly under the android section in app/src/build.gradle
  packagingOptions{
    doNotStrip "*/armeabi/*.so"
    doNotStrip "*/armeabi-v7a/*.so"
    doNotStrip "*/x86/*.so"
    doNotStrip "*/x86_64/*.so"
  }
}
```

Next, you will need to generate and upload `.sym` files to BugSplat. To generate symbols for your Android NDK library you will need to build the Breakpad tool `dump_syms` on a Linux machine. For more information about building Breakpad tools on Linux please see this [document](../cross-platform/crashpad/how-to-build-google-crashpad.md#generating-symbols).

Once you've built `dump_syms` on a Linux system, run `dump_syms` on a Linux machine passing it the path to your Android library:

```bash
./dump_syms path/to/app/build/intermediates/merged_native_libs/debug/out/lib/x86/my-lib.so > /my-lib.so.sym
```

You can also run the Linux version of `dump_syms` using compatible Linux emulator on [macOS](https://github.com/linux-noah/noah) or [Windows](https://docs.microsoft.com/en-us/windows/wsl/install-win10). Examples of how to run a Linux build of `dump_syms` on macOS and Windows can be found in the [tools](https://github.com/BugSplat-Git/AndroidCrasher/tree/master/tools) section of the AndroidCrasher repository.

If you're developing on an macOS or Linux system, build the Breakpad tool `symupload` on your local system. Upload the generated `.sym` file by running `symupload`. Be sure to replace the `{{database}}`, `{{application}}` and `{{version}}` with the values you used in the Configuring Crashpad section:

```bash
symupload "/path/to/my-lib.so.sym" "https://{{database}}.bugsplat.com/post/bp/symbol/breakpadsymbols.php?appName={{application}}&appVer={{version}}"
```

If you're developing on Windows, `symupload` will not upload your Android `.sym` files. As an alternative, the AndroidCrasher [tools](https://github.com/BugSplat-Git/AndroidCrasher/tree/master/tools) folder provides and example PowerShell [script](https://github.com/BugSplat-Git/AndroidCrasher/blob/master/tools/symupload-windows.ps1) that will upload `.sym` files to BugSplat.

After each release build you'll need to generate and upload `.sym` files making sure to increment the version number each time. The version number from the Configuring Crashpad section must match the version number in your upload URL.

### Generating a Crash Report

Force a crash in your application after Crashpad has been initialized:

```cpp
*(volatile int *)0 = 0;
```

After you've submitted a crash report, navigate to the [Crashes](https://app.bugsplat.com/v2/crashes?database=Fred\&c0=appName\&f0=CONTAINS\&v0=AndroidCrasher) page. Click the link in the `ID` column to see the details of your crash report. The following image is from our sample `AndroidCrasher` application:

![BugSplat Android NDK Crash](../../../../.gitbook/assets/android-crash.png)
