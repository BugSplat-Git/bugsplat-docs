# Breakpad \(Deprecated\)

{% hint style="danger" %}
**Breakpad is the predecessor of Crashpad**. If you are configuring a new integration, please consider using our [Crashpad integration](https://www.bugsplat.com/docs/sdk/crashpad) instead.
{% endhint %}

## Overview

Breakpad is the predecessor of Crashpad. If you are configuring a new integration, please consider using our [Crashpad integration](https://www.bugsplat.com/docs/sdk/crashpad) instead.

Google Breakpad is a crash reporting tool built by Google in C++. It allows you to submit minidumps to a configured URL as a crash happens. With Breakpad you can process crashes for [Windows](https://github.com/google/breakpad/blob/master/docs/windows_client_integration.md), [OS X](https://github.com/google/breakpad/blob/master/docs/mac_breakpad_starter_guide.md), and [Linux](https://github.com/google/breakpad/blob/master/docs/linux_starter_guide.md) applications.

In a few simple steps, your Breakpad-enabled application can be configured to send crash reports to BugSplat. This allows you to take advantage of BugSplat's reporting mechanisms.

For more information see Google's Integration Overview [here](https://chromium.googlesource.com/breakpad/breakpad/+/master/docs/getting_started_with_breakpad.md).

## Prerequisites

Before continuing with the integration please complete the following tasks:

* Create a new [database](https://app.bugsplat.com/v2/company) for your application
* Clone the [source](https://chromium.googlesource.com/breakpad/breakpad/)
* Build the exception\_handler and crash\_report\_sender libraries and integrate them into your application
* Build the dump\_syms and symupload tools

## Configuration

1.Configure Breakpad to post crashes to `https://{database}.bugsplat.com/post/bp/crash/postBP.php`. Be sure to specify your own value for the {database} portion of the URL, which corresponds to the BugSplat database used to store your crash reports.

```cpp
bool minidumpCallback(
  const wchar_t* dump_path,
  const wchar_t* minidump_id,
  void* context,
  EXCEPTION_POINTERS* exinfo,
  MDRawAssertionInfo* assertion,
  bool succeeded
)
{
  ...
  wstring bugSplatUrl = L"https://" + database + L".bugsplat.com/post/bp/crash/postBP.php";
  ReportResult reportResult = reportSender->SendCrashReport(bugSplatUrl, parameters, files, &exceptionCode);
}
```

2. Configure the Breakpad POST parameters `prod` for the BugSplat application name and `ver` for the BugSplat application version. You can optionally specify values for the POST parameters `email` and `comments`, which will be tracked with each crash report. Also, configure the `files` parameter as shown below.

```cpp
bool minidumpCallback(
  const wchar_t* dump_path,
  const wchar_t* minidump_id,
  void* context,
  EXCEPTION_POINTERS* exinfo,
  MDRawAssertionInfo* assertion,
  bool succeeded
)
{
  ...
  map<wstring, wstring> parameters;
  parameters[L"prod"] = L"MyApp";
  parameters[L"ver"] = L"1.0";
  parameters[L"email"] = L"fred@bugsplat.com";
  parameters[L"comments"] = L"BugSplat rocks!";
  files[L"upload_file_minidump"] = MinidumpDescriptor.path();
  ReportResult reportResult = reportSender->SendCrashReport(bugSplatUrl, parameters, files, &exceptionCode);
}
```

3. Create a new symbol store on our [Symbols](https://app.bugsplat.com/v2/symbols/) page. You should do this for each released version of your product in order to ensure your crash reports contain function names and line numbers.

4. Use dump\_syms to generate .sym files for your application. You need to do this for each release version of your product.

5. Upload your application's .sym files to BugSplat via the symupload tool, or via our manual symbol upload page. To view the manual symbol upload page, select one of your symbol stores on the [Symbols](https://app.bugsplat.com/v2/symbols/) page, then click the "Upload new symbol files" link. Alternatively, you can use symupload to automate the symbol upload process. Run the following symupload command replacing `{database}`, `{appName}` and `{appVersion}` with values specific to your BugSplat database and symbol store:

```bash
symupload file.sym "https://{database}.bugsplat.com/post/bp/symbol/breakpadsymbols.php?appName={appName}&appVer={appVersion}"
```

6. Trigger a crash in your application. The following code snippet can be used to generate an EXCEPTION\_ACCESS\_VIOLATION\_WRITE crash:

```cpp
int nullVal;
void crash()
{
  *(volatile int *)0 = nullVal;
}
```

### Processing as Windows Native

BugSplat can process Breakpad crashes reported from Windows operating systems with our Windows backend, rather than the Breakpad backend. The advantage to this approach is that BugSplat will be able to display function arguments and local variables for each resolved stack frame. Another advantage of this approach is that our backend will automatically resolve Windows OS symbols.

To configure your Breakpad crashes to be processed by our Windows backend, create unique AppName/AppVersion combinations for the Windows versions of your application and upload .pdb, .dll and .exe files \(rather than .sym files\). The presence of .pdb, .dll or .exe files in the symbol store is what triggers the use of the Windows backend. Uploading Windows symbols can be done via our manual symbol upload page or our automated tool [SendPdbs](https://www.bugsplat.com/docs/faq/sendpdbs).

### Additional Considerations

Crashes can be posted manually using our test page at `https://{database}.bugsplat.com/post/bp/crash/Native/index.php`. Replace `{database}` with the name of your BugSplat database. Viewing the source HTML of that page may help with the configuration.

The BugSplat database for your crash reports is created on the [Company](https://app.bugsplat.com/v2/company) page. Typically you will create a new database for each major release of your product.

If you are sending symbols from symupload on OS X there is no command line option to increase the upload timeout. We have created a [fork of symupload](https://github.com/BugSplat-Git/breakpad/commit/b823d9128884051627874a780296edef1cf6acac) with the timeout increased to 100 seconds \(from 10 seconds\). You need to use a modified version of symupload to upload files larger than 100 MB. You can download the modified archive [here](https://s3.amazonaws.com/bugsplat-public/symupload.xcarchive.zip).
