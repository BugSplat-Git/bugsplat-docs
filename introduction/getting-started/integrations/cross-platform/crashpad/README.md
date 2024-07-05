# Crashpad

## Overview

{% hint style="warning" %}
**Want help building Crashpad?** View our step-by-step guide to help you more quickly get started [here](how-to-build-google-crashpad.md).
{% endhint %}

Crashpad is the latest open-source crash reporting tool built by Google and is the successor to the popular Breakpad crash reporter. Crashpad allows you to submit minidumps to a configured URL after a crash occurs in your product. The official Crashpad documentation is available [here](https://chromium.googlesource.com/crashpad/crashpad/+/master/README.md). Crashpad and Breakpad are 'wire compatible.' The crash reports created by both systems are processed similarly on our backend.

## Tutorial

To begin, [download](https://app.bugsplat.com/browse/download\_item.php/?item=crashpad) and unzip the BugSplat Crashpad software development kit. The download contains a sample Crashpad application and a compiled version of Crashpad for Windows.

It's also possible to download and build Crashpad yourself. This step is required if you are targeting an OS other than Windows. See our [Building Crashpad](how-to-build-google-crashpad.md) doc for our step-by-step guide to building Crashpad.

To get a feel for the BugSplat service, experiment with the myCrashpadCrasher sample application before enabling your application. You can find the Visual Studio project file located in your download folder at `...\BugSplatCrashpad\BugSplat\samples\myCrashpadCrasher\myCrashpadCrasher.vcxproj.`

Run the sample application without the debugger attached to post a crash report to our public "fred@bugsplat.com" database. To view the report, log in to the public database using the account "fred@bugsplat.com" and the password "Flintstone."

### Integrating Crashpad

Follow the myCrashpadCrasher pattern to enable Crashpad in your application.

#### Step 1

Add the following includes:

```cpp
#include "client/crashpad_client.h"
#include "client/crash_report_database.h"
#include "client/settings.h"
```

#### Step 2

Copy the `initializeCrashpad` and `GetCrashpadHandlerPath` methods from the BugSplat sample. The Crashpad `url` and parameters `format`, `database`, `product` and `version` are required to upload crash reports to BugSplat. You can optionally specify values for the parameters `user`, `list_annotations`, and `key` which will be tracked with each crash report.

```cpp
CrashpadClient *initializeCrashpad(char *dbName, char *appName, char *appVersion)
{
  // Get directory where the crashpad_handler.exe file lives, sample implementation of this method is provided in the myCrashpadCrasher sample
  std::wstring handlerPath = GetCrashpadHandlerPath();

  if (INVALID_FILE_ATTRIBUTES == GetFileAttributesW(handlerPath.c_str()))
  {
    return NULL;
  }

  // Ensure that handler is shipped with your application
  base::FilePath handler(handlerPath);

  // Directory where reports will be saved. Important! Must be writable or crashpad_handler will crash.
  base::FilePath reportsDir(L"./crashpad"); 

  // Directory where metrics will be saved. Important! Must be writable or crashpad_handler will crash.
  base::FilePath metricsDir(L"./crashpad"); 

  // Configure url with your BugSplat database
  std::string url;
  url = "https://";
  url += dbName;
  url += ".bugsplat.com/post/bp/crash/crashpad.php";

  // Metadata that will be posted to BugSplat
  std::map<std::string, std::string> annotations;
  annotations["format"] = "minidump";                 // Required: Crashpad setting to save crash as a minidump
  annotations["database"].assign(dbName);             // Required: BugSplat database
  annotations["product"].assign(appName);             // Required: BugSplat application name
  annotations["version"].assign(appVersion);          // Required: BugSplat version number
  annotations["key"] = "Sample key";                  // Optional: BugSplat key field
  annotations["user"] = "fred@bugsplat.com";          // Optional: BugSplat user email
  annotations["list_annotations"] = "Sample comment";	// Optional: BugSplat crash description

  // Disable crashpad rate limiting so that all crashes have dmp files
  std::vector<std::string> arguments;
  arguments.push_back("--no-rate-limit");

  // Initialize crashpad database
  std::unique_ptr<CrashReportDatabase> database = crashpad::CrashReportDatabase::Initialize(reportsDir);
  if (database == NULL) return false;

  // Enable automated crash uploads
  Settings *settings = database->GetSettings();
  if (settings == NULL) return false;
  settings->SetUploadsEnabled(true);

  // Files to upload with the crash report - default bundle size limit is 20MB
  std::vector<base::FilePath> attachments;
  base::FilePath attachment(L"./attachment.txt");
  attachments.push_back(attachment);

  // Start crash handler
  CrashpadClient *client = new CrashpadClient();
  bool status = client->StartHandler(handler, reportsDir, metricsDir, url, annotations, arguments, true, true, attachments);
  if (status == false) return NULL;

  // Wait for handler to initialize
  status = client->WaitForHandlerStart(INFINITE);
  if (status == false) return NULL;

  return client;
}
```

#### Step 3

Call `initializeCrashpad` using your own parameters for the BugSplat `dbName`, `appName`, and `appVersion`.

```cpp
char *dbName = (char *)"fred";
char *appName = (char *)"myCrashpadCrasher";
char *appVersion = (char *)"1.0";

initializeCrashpad(dbName, appName, appVersion);
```

#### Step 4

Link your application with the appropriate version of the Crashpad libraries `client.lib`, `base.lib`, and`util.lib`. BugSplat supplies builds for Debug/Release (x86) and Debug\_x64/Release\_x64 versions of the Crashpad libraries.

#### Step 5

You will need to generate and upload `.sym` files to BugSplat to generate function names and line numbers in the stack traces of your crash reports. BugSplat's [symbol-upload](https://github.com/BugSplat-Git/symbol-upload) tool can be used conveniently to generate and upload `.sym` files with a single terminal command. Download a copy of symbol-upload from [GitHub](https://github.com/BugSplat-Git/symbol-upload) or install it via [npm](https://npmjs.com/package/@bugsplat/symbol-upload).

Once you have symbol-upload downloaded or installed, modify the following command to suit your needs.

```bash
symbol-upload-windows -b "fred" ^
    -a "myCrashpadCrasher" ^
    -v "1.0" ^
    -u "fred@bugsplat.com" ^
    -p "Flintstone" ^
    -d "C:\\path\\to\\build\\output" ^
    -f "*.{pdb,exe,dll}" ^
    --dumpSyms
```

#### Step 6

Trigger a crash in your application. The crash report should be available immediately on the BugSplat website.

## Additional Considerations

### Databases

The BugSplat database for your crash reports is created on the [Manage Database](https://app.bugsplat.com/v2/company/databases) page in Settings. Typically, you will create a new database for each major release of your product.

### Optimizations

Compiler optimizations can cause a mismatch between the line numbers in crash reports and the actual line numbers in your code. BugSplat recommends turning off compiler optimizations to ensure that the line numbers in your crash reports match the line numbers in your code. To turn off optimizations in Visual Studio, right-click your project and navigate to **Properties > C/C++ > Optimization > Optimization,** and set the value to **Disabled (/Od)**.

###
