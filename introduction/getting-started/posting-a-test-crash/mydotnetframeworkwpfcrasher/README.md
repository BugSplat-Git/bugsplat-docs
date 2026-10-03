---
description: >-
  Testing .NET Framework crashes, hangs, and handled exceptions with the sample
  WPF application 'MyDotNetFrameworkWpfCrasher'
---

# MyDotNetFrameworkWpfCrasher (.NET Framework)

Before you enable BugSplat in your .NET Framework application, you may want to take a moment to experiment with our `MyDotNetFrameworkWpfCrasher` sample, a WPF (.NET Framework 4.7.2) app with a button for each kind of report [BugSplat for .NET Framework](../../integrations/desktop/windows-dot-net-framework.md) sends.

To get started, download the BugSplat SDK for .NET by clicking [here](https://app.bugsplat.com/browse/download_item.php?item=dotnet), then unzip it.

1. Open `MyDotNetFrameworkWpfCrasher.sln` with Visual Studio 2022+.
2. Set your database in `Samples\MyDotNetFrameworkWpfCrasher\App.xaml.cs` (`App.Database`), and optionally `App.AppName` and `App.Version`.
3. Create a Client ID and Client Secret pair for your BugSplat database on the [Integrations](https://app.bugsplat.com/v2/settings/database/integrations) page.
4. Create a file `Samples\MyDotNetFrameworkWpfCrasher\Scripts\env.ps1` and populate it with the following (being sure to substitute your `your-client-id` and `your-client-secret` values from the previous step):

```powershell
$BUGSPLAT_CLIENT_ID = "your-client-id"
$BUGSPLAT_CLIENT_SECRET = "your-client-secret"
```

5. Build the solution for **x64**. A post-build step uploads the sample's executable, DLLs, and `.pdb` files to BugSplat, so its crashes show file names and line numbers. The solution also builds `MyDotNetCrasherNative`, the small C++ library the Mixed-Mode and Heap Corruption buttons call into.
6. Run the sample outside of the Visual Studio debugger (Ctrl+F5). This is important since the debugger interferes with BugSplat's exception handling.
7. Click a button:

| Button | What it does |
| --- | --- |
| **Crash** | Throws an unhandled exception on the UI thread. BugSplat captures a minidump and shows its crash dialog. |
| **Non-Crash Error** | Catches an exception and reports it with `BugSplat.Post`. The app keeps running. |
| **User Feedback** | Sends a note with `BugSplat.PostFeedback` and shows the resulting report id. |
| **Hang** | Freezes the UI thread until BugSplat's hang detection reports the hang (within about 10 seconds) and ends the app. |
| **Mixed-Mode Crash** | Calls into C++ code that crashes, so the report has one call stack that crosses from C# into C++. |
| **Heap Corruption** | Calls into C++ code that frees the same memory twice. Windows fail-fasts the app through Windows Error Reporting, so this button is disabled until `BugSplatWer.dll` is registered (see below). |

8. When the crash dialog appears, enter some descriptive text to help you identify the crash and click **Send Error Report**.
9. Navigate to the BugSplat [Dashboard](https://app.bugsplat.com/v2/dashboard) and click the link in the ID column to view details about your crash, including the full symbolicated call stack and various crash metadata.

### Heap Corruption and Windows Error Reporting

Heap corruption bypasses every in-process handler, so BugSplat captures it through its Windows Error Reporting helper, `BugSplatWer.dll`, which Windows loads only when its path is in the registry. From an elevated prompt, register the copy next to the sample's executable, then restart the sample:

```
reg add "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\RuntimeExceptionHelperModules" /v "<path to the sample's bin folder>\BugSplatWer.dll" /t REG_DWORD /d 0 /f
```

Until the entry exists, the Heap Corruption button is dimmed, and hovering over it explains why.

Finally, explore how each button is implemented in the sample's source code, and see [BugSplat for .NET Framework](../../integrations/desktop/windows-dot-net-framework.md) to integrate BugSplat into your own application.
