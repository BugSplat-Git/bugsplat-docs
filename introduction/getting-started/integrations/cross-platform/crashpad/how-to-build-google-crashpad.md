# How to Build Google Crashpad

## Overview

Crashpad is a cross-platform system for end-to-end crash reporting. Crashpad supports reporting of native crashes on a variety of operating systems including Windows, macOS, Linux, Android, and iOS. Crashpad also provides tools such as `dump_syms`, `symupload` and `minidump_stackwalk` that provide developers with function names, file names, and line numbers in their crash reports. Integrating with Crashpad helps software engineers find and fix program crashes in order to develop more stable applications.

{% embed url="https://bugsplat.wistia.com/medias/bh4l456ydm" %}

## Installing depot\_tools

The Chromium [depot\_tools](https://chromium.googlesource.com/chromium/tools/depot\_tools.git) are a set of tools that are required to build Crashpad. The documentation claims that Python 2.7 is a requirement for depot\_tools, however since checking out Crashpad uses only a subset of the depot tools Python 3+ worked fine for BugSplat.

Run the following terminal commands below to clone `depot_tools` and add the tools to your system PATH variable. Be sure to change `/path/to/depot_tools` to the path where you cloned depot\_tools.

### **macOS**

```bash
git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git 
sudo echo "export PATH=/path/to/depot_tools:$PATH" >> ~/.zshrc
```

### **Linux**

```
git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git 
sudo echo "export PATH=/path/to/depot_tools:$PATH" >> ~/.bashrc
```

### **Windows**

```bash
git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git
setx path "%path%;C:\path\to\depot_tools"
```

## Getting the Crashpad Source

The Crashpad source code can be found [here](https://chromium.googlesource.com/crashpad/crashpad). Crashpad’s dependencies are managed by `gclient` instead of git submodules, so it is best to use `fetch` to get the source code.

### **Initial Checkout**

```bash
mkdir ~/crashpad
cd ~/crashpad
fetch crashpad
```

### **Subsequent Checkouts**

```bash
cd ~/crashpad/crashpad
git pull -r
gclient sync
```

## Building Crashpad

Crashpad uses `gn` to generate `ninja` build files.

{% hint style="info" %}
By default `gn` generates a configuration for building static libraries. If you would like to build dynamic libraries see this [post](https://stackoverflow.com/questions/55302553/how-to-build-dynamic-shared-libraries-of-crashpad) on Stack Overflow.
{% endhint %}

### **Generating Build Configuration**

```bash
cd ~/crashpad/crashpad
gn gen out/Default
```

### **Building with Ninja**

```bash
ninja -C out/Default
```

## Integrating Crashpad

Building Crashpad generates several files that need to be linked with an application in order to generate crash reports.

### **macOS & Linux**

At a minimum, macOS and Linux applications need to be linked with `out/Default/obj/client/libcommon.a`, `out/Default/obj/client/libclient.a`, `out/Default/obj/util/libutil.a`, and `out/Default/obj/third_party/mini_chromium/mini_chromium/base/libbase.a`.

MacOS application will need to link with `out/Default/obj/util/libmig_output.a` as well.

When building Linux applications, `libbase.a` needs to be the last Crashpad file specified in the build arguments or the application will not build.

Additionally, `~/crashpad/crashpad` and `~/crashpad/crashpad/third_party/mini_chromium/mini_chromium` need to be added as include directories.

Finally, `out/Default/crashpad_handler` needs to be deployed with the application and accessible at runtime.

### **Windows**

At a minimum, Windows applications need to be linked with `out\Default\obj\client\common.lib`, `out\Default\obj\client\client.lib`, `out\Default\obj\util\util.lib`, and `out\Default\obj\third_party\mini_chromium\mini_chromium\base\base.lib.`

Finally, `out\Default\crashpad_handler.exe` needs to be deployed with the application and accessible at runtime.

## Configuring Crashpad

Add the following includes to the entry point of the application.

```cpp
#include "client/crashpad_client.h"
#include "client/crash_report_database.h"
#include "client/settings.h"
```

Add a typedef for `StringType`, add using statements for the `base`, `crashpad` and `std` namespace and declare the following methods at the top of the entry point of the application.

```bash
#if defined(__APPLE__)
typedef std::string StringType;
#elif  defined(__linux__)
typedef std::string StringType;
#elif defined(_MSC_VER)
typedef std::wstring StringType;
#endif

using namespace base;
using namespace crashpad;
using namespace std;

bool initializeCrashpad(void);
StringType getExecutableDir(void);
```

Implement the `initializeCrashpad` method replacing the file paths with valid values.

```cpp
bool initializeCrashpad() {
  // Get directory where the exe lives so we can pass a full path to handler, reportsDir, metricsDir and attachments
  StringType exeDir = getExecutableDir();

  // Ensure that handler is shipped with your application
  FilePath handler(exeDir + "/path/to/crashpad_handler");

  // Directory where reports will be saved. Important! Must be writable or crashpad_handler will crash.
  FilePath reportsDir(exeDir + "/path/to/crashpad");

  // Directory where metrics will be saved. Important! Must be writable or crashpad_handler will crash.
  FilePath metricsDir(exeDir + "/path/to/crashpad");

  // Configure url with BugSplat’s public fred database. Replace 'fred' with the name of your BugSplat database.
  StringType url = "https://fred.bugsplat.com/post/bp/crash/crashpad.php";

  // Metadata that will be posted to the server with the crash report map
  map<StringType, StringType> annotations;
  annotations["format"] = "minidump";           // Required: Crashpad setting to save crash as a minidump
  annotations["database"] = "fred";             // Required: BugSplat appName
  annotations["product"] = "myCrashpadCrasher"; // Required: BugSplat appName
  annotations["version"] = "1.0.0";             // Required: BugSplat appVersion
  annotations["key"] = "Sample key";            // Optional: BugSplat key field
  annotations["user"] = "fred@bugsplat.com";    // Optional: BugSplat user email
  annotations["list_annotations"] = "Sample comment"; // Optional: BugSplat crash description

  // Disable crashpad rate limiting so that all crashes have dmp files
  vector<StringType> arguments; 
  arguments.push_back("--no-rate-limit");

  // Initialize Crashpad database
  unique_ptr<CrashReportDatabase> database = CrashReportDatabase::Initialize(reportsDir);
  if (database == NULL) return false;

  // File paths of attachments to uploaded with minidump file at crash time - default upload limit is 2MB
  vector<FilePath> attachments;
  FilePath attachment(exeDir + "/path/to/attachment.txt");
  attachments.push_back(attachment);

  // Enable automated crash uploads
  Settings *settings = database->GetSettings();
  if (settings == NULL) return false;
  settings->SetUploadsEnabled(true);

  // Start crash handler
  CrashpadClient *client = new CrashpadClient();
  bool status = client->StartHandler(handler, reportsDir, metricsDir, url, annotations, arguments, true, true, attachments);
  return status;
}
```

Next, implement the platform-specific `getExecutableDir` method.

### **macOS**

```cpp
#include <mach-o/dyld.h>
#include <vector>

StringType getExecutableDir() {
  unsigned int bufferSize = 512;
  vector<char> buffer(bufferSize + 1);

  if (_NSGetExecutablePath(&buffer[0], &bufferSize)) {
    buffer.resize(bufferSize);
    _NSGetExecutablePath(&buffer[0], &bufferSize);
  }

  char *lastForwardSlash = strrchr(&buffer[0], '/');
  if (lastForwardSlash == NULL) return NULL;
  *lastForwardSlash = 0;

  return &buffer[0];
}
```

### **Linux**

```cpp
#include <stdio.h>
#include <unistd.h>
#define MIN(x, y) (((x) < (y)) ? (x) : (y))

StringType getExecutableDir() {
  char pBuf[FILENAME_MAX];
  int len = sizeof(pBuf);
  int bytes = MIN(readlink("/proc/self/exe", pBuf, len), len - 1);
  if (bytes >= 0) {
    pBuf[bytes] = '\0';
  }

  char* lastForwardSlash = strrchr(&pBuf[0], '/');
  if (lastForwardSlash == NULL) return NULL;
  *lastForwardSlash = '\0';

  return pBuf;
}
```

### Windows

```cpp
StringType getExecutableDir() {
  HMODULE hModule = GetModuleHandleW(NULL);
  WCHAR path[MAX_PATH];
  DWORD retVal = GetModuleFileNameW(hModule, path, MAX_PATH);
  if (retVal == 0) return NULL;

  wchar_t *lastBackslash = wcsrchr(path, '\\');
  if (lastBackslash == NULL) return NULL;
  *lastBackslash = 0;

  return path;
}
```

Finally, call the `initializeCrashpad` method at the entry point of the application.

```cpp
int main(int argc, char *argv[]) {
  initializeCrashpad();
}
```

## Generating Symbols

Generating sym files requires the `dump_syms` tool from the repository of Crashpad’s predecessor, [Breakpad](https://chromium.googlesource.com/breakpad/breakpad/). Dump\_syms creates [sym files](https://github.com/google/breakpad/blob/master/docs/symbol\_files.md) from executable binaries so that minidumps can be symbolicated to determine the function names, file names, and line numbers in the call stack.

```bash
mkdir ~/breakpad
cd breakpad
fetch breakpad
```

### **macOS**

Build the application with symbolic information (preferably in a separate dSYM file) in order to get fully symbolicated crash reports.

Next, build the Xcode project located at `src/src/tools/mac/dump_syms/dump_syms.xcodeproj`. Switch the configuration to dump\_syms and build the project. The report navigator tab (icon looks like a chat bubble in Xcode 11) will show the file system location with the compiled executable. Run the dump\_syms executable.

```bash
dump_syms -g "/path/to/myApp.app.dSYM" "/path/to/myApp.app/Contents/MacOS/myApp" > myApp.sym
```

### **Linux**

Build the application with symbolic information and a build identifier. Using `clang` this means building with the `-g` and `-Wl,--build-id` arguments.

Next, run `./configure && make` in the Breakpad directory. This will generate `dump_syms`, `symupload` and `minidump_stackwalk`.

```bash
dump_syms /path/to/myApp.out > myApp.out.sym
```

### **Windows**

The dump\_syms functionality is built into the `symupload` utility and can be skipped if the application will be symbolicated remotely. Run `dump_syms` only if the application will be symbolicated locally or the sym file will be uploaded to a remote server via some means other than symupload.

In order for `dump_syms.exe` to generate the correct output, applications must be built with symbolic information so that each exe and dll file generates a corresponding pdb file. Generated pdb files must contain full debug information. With Visual Studio, full debug information can be generated with the `/Zi` compiler argument and the `/DEBUG:FULL` linker argument. Failure to specify either the `/Zi` or `/DEBUG:FULL` arguments will result in dump\_syms outputting incorrect sym file data. Additionally the output pdb file must be in the same folder as the corresponding exe or dll file otherwise dump\_syms.exe will fail.

In order to run dump\_syms.exe a copy of `msdia140.dll` must be placed in the same folder. If [Visual Studio](https://docs.microsoft.com/en-us/visualstudio/productinfo/2017-redistribution-vs#dia-sdk) is installed this file can be found at `[VisualStudioFolder]\DIA SDK\bin\amd64\msdia140.dll`. Copy msdia140.dll into the same folder as dump\_syms.exe and run dump\_syms.exe.

```bash
symupload.exe "path/to/myApp.exe" "https://fred.bugsplat.com/post/bp/symbol/breakpadsymbols.php?appName=myApp&appVer=1.0.0"
```

Dump\_syms can be built from source so that the debugger can be used for troubleshooting. To build `dump_syms` clone the [gyp repository](https://github.com/chromium/gyp) and run `python setup.py install`. Next, run `gyp ~\breakpad\src\tools\windows\dump_syms\dump_syms.gyp` and use Visual Studio to build the sln file generated by gyp. If the build fails with an error that unique\_pointer is not part of std add `#include <memory>` to the top of the file that contains the error and rebuild.

## Uploading Symbols

The `symupload` tool is also part of the [Breakpad](https://chromium.googlesource.com/breakpad/breakpad/) repository and can be used to upload sym files to your server. Symbols need to be uploaded to a server in order for it to correctly symbolicate a minidump file.

### **macOS**

Build the Xcode project located at `src/src/tools/mac/symupload/symupload.xcodeproj`. The report navigator tab (icon looks like a chat bubble in Xcode 11) will show you the file system location with the compiled executable. Copy the symupload file into your project and run the executable wrapping the arguments to symupload in quotes.

```bash
symupload "/path/to/myApp.sym" "https://fred.bugsplat.com/post/bp/symbol/breakpadsymbols.php?appName=myApp&appVer=1.0.0"
```

### **Windows**

In order to use symupload applications must be built with symbolic information so that each exe and dll file generates a corresponding pdb file. Generated pdb files must contain full debug information. Full debug information can be generated with the `/Zi` compiler argument and the `/DEBUG:FULL` linker argument. Failure to specify either the `/Zi` or `/DEBUG:FULL` arguments will result in symupload failing entirely. The output pdb file must be in the same folder as the corresponding exe or dll file.

In order to run symupload.exe a copy of `msdia140.dll` must be placed in the same folder. If [Visual Studio](https://docs.microsoft.com/en-us/visualstudio/productinfo/2017-redistribution-vs#dia-sdk) is installed this file can be found at `[VisualStudioFolder]\DIA SDK\bin\amd64\msdia140.dll`. Copy msdia140.dll into the same folder as symupload.exe and run symupload.exe.

```bash
dump_syms.exe "path/to/myApp.exe" > myApp.pdb.sym
```

Symupload can be built from source so that the debugger can be used for troubleshooting. To build symupload clone the [gyp repository](https://github.com/chromium/gyp) and run `python setup.py install`. Next, run `gyp ~\breakpad\src\tools\windows\symupload\symupload.gyp` and use Visual Studio to build the sln file generated by gyp. If the build fails with an error that unique\_pointer is not part of std add `#include <memory>` to the top of the file that contains the error and rebuild.

## Symbolicating Crash Reports

`Minidump_stackwalk` is another tool in the [Breakpad](https://chromium.googlesource.com/breakpad/breakpad/) repository that is responsible for the symbolication of minidump files. In order to correctly symbolicate minidumps, sym files need to be nested at least 2 folders deep. The topmost parent folder’s name must equal the sym files module name. The first child folder’s name must equal the module id. Additionally, the sym file name must also match the module name. The module id and module name can be found in the [module record](https://github.com/google/breakpad/blob/master/docs/symbol\_files.md#module-records) of the sym file.

For example the module `myApp` with the module id `1A67F3DEAACA3B209D9992871B2620AA0` must be located at `/path/to/symbols/myApp/1A67F3DEAACA3B209D9992871B2620AA0/myApp.sym`.

### **macOS & Linux**

Minidump\_stackwalk is built when building [Breakpad](https://chromium.googlesource.com/breakpad/breakpad#getting-started-from-master).

```
cd ~/breakpad/src && configure && make
```

Run `minidump_stackwalk` passing it a path to a dmp file and a path to a symbols directory. The folders in the symbols directory need to be laid out following the pattern `module_name/module_id/module_name.sym`:

```
minidump_stackwalk -m "/path/to/minidump.dmp" "/path/to/symbols"
```

### **Windows**

Minidump\_stackwalk is part of the Breakpad `processor` sln which is generated by `gyp`

```
gyp ~/breakpad/src/src/processor/processor.gyp
```

At the time of writing the `processor.sln` file will not build on Windows 10 with Visual Studio 2019. However, minidumps generated on the Windows platform can be symbolicated on macOS or by a 3rd party service such as [BugSplat](https://www.bugsplat.com/). Additionally, minidumps generated by the Crashpad library can also be symbolicated via [Debugging Tools](https://docs.microsoft.com/en-us/windows-hardware/drivers/debugger/) for Windows given the `.exe`, `.dll` and `.pdb` files are made available to WinDBG.

## Troubleshooting

Most issues symbolicating dump files can be traced back to mismatched module ids. Anything that modifies a given executable (code-signing, anti-cheat) must be performed before `dump_syms` and `symupload` are run. Every time an executable is modified `dump_syms` and `symupload` need to be re-run. The name and id of the module loaded at runtime can be found in the `minidump_stackwalk` output. The module name and id must match the module name and id of the generated sym file in order for `minidump_stackwalk` to correctly symbolicate the minidump file.

## References

1. [https://commondatastorage.googleapis.com/chrome-infra-docs/flat/depot\_tools/docs/html/depot\_tools\_tutorial.html#\_setting\_up](https://commondatastorage.googleapis.com/chrome-infra-docs/flat/depot\_tools/docs/html/depot\_tools\_tutorial.html#\_setting\_up)
2. [https://chromium.googlesource.com/crashpad/crashpad/+/HEAD/doc/developing.md](https://chromium.googlesource.com/crashpad/crashpad/+/HEAD/doc/developing.md)
3. [https://groups.google.com/a/chromium.org/forum/#!topic/crashpad-dev/XVggc7kvlNs](https://groups.google.com/a/chromium.org/forum/#!topic/crashpad-dev/XVggc7kvlNs)
4. [https://docs.bugsplat.com/introduction/getting-started/integrations/cross-platform/crashpad](https://docs.bugsplat.com/introduction/getting-started/integrations/cross-platform/crashpad)
5. [https://github.com/google/breakpad/blob/master/docs/getting\_started\_with\_breakpad.md#build-process-specificssymbol-generation](https://github.com/google/breakpad/blob/master/docs/getting\_started\_with\_breakpad.md#build-process-specificssymbol-generation)
6. [https://chromium.googlesource.com/breakpad/breakpad/+/master/docs/getting\_started\_with\_breakpad.md#build-process-specifics\_symbol-generation](https://chromium.googlesource.com/breakpad/breakpad/+/master/docs/getting\_started\_with\_breakpad.md#build-process-specifics\_symbol-generation)
7. [https://www.chromium.org/developers/decoding-crash-dumps](https://www.chromium.org/developers/decoding-crash-dumps)
