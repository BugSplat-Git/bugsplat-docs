---
description: >-
  Testing .NET crashes, hangs, handled exceptions, and user feedback with the
  sample WinUI 3 application 'MyDotNetWinUI3Crasher'
---

# MyDotNetWinUI3Crasher (.NET)

Before you enable BugSplat in your .NET application, you may want to take a moment to experiment with our `MyDotNetWinUI3Crasher` sample, a WinUI 3 (.NET 10) app with a button for each kind of report [BugSplat for .NET](../../integrations/desktop/bugsplat-for-dot-net.md) sends. It's the windowed companion of the [MyDotNetCrasher](../mydotnetcrasher/) console sample, and the .NET counterpart of the .NET Framework [MyDotNetFrameworkWpfCrasher](../mydotnetframeworkwpfcrasher/).

To get started, download the BugSplat SDK for .NET by clicking [here](https://app.bugsplat.com/browse/download_item.php?item=dotnet), then unzip it. You'll need the [.NET 10 SDK](https://dotnet.microsoft.com/download/dotnet/10.0) and Visual Studio with the WinUI application development workload, plus the C++ desktop workload and the `v143` build tools for the Mixed-Mode Crash button's `MyDotNetCrasherNative` library.

1. Open `MyDotNetWinUI3Crasher.sln` with Visual Studio.
2. Set your database in `Samples\MyDotNetWinUI3Crasher\App.xaml.cs` (`App.Database`), and optionally `App.Application` and `App.Version`.
3. Create a Client ID and Client Secret pair for your BugSplat database on the [Integrations](https://app.bugsplat.com/v2/database/integrations#oauth) page.
4. Create a file `Samples\MyDotNetWinUI3Crasher\Scripts\env.ps1` and populate it with the following (being sure to substitute your `your-client-id` and `your-client-secret` values from the previous step), or set the `BUGSPLAT_CLIENT_ID` and `BUGSPLAT_CLIENT_SECRET` environment variables instead:

```powershell
$BUGSPLAT_CLIENT_ID = "your-client-id"
$BUGSPLAT_CLIENT_SECRET = "your-client-secret"
```

5. Build the solution for **x64**. It builds `MyDotNetCrasherNative` first, then the app. A post-build step uploads the sample's executable, DLLs, and `.pdb` files to BugSplat with `Scripts\SymbolUpload.ps1`, so its crashes show file names and line numbers. If the upload fails, so does the build; to build without uploading, pass `/p:BugSplatSymbolUpload=false`. The app is unpackaged and self-contained, so it runs without an MSIX package, a certificate, or a separate Windows App SDK runtime install.
6. Register `BugSplatWer.dll` with Windows Error Reporting (see below). A WinUI 3 app's crashes are reported only through it, so until it's registered the app warns you at startup.
7. Run the sample outside of the Visual Studio debugger (Ctrl+F5, or `dotnet run -c Debug -p:Platform=x64` from `Samples\MyDotNetWinUI3Crasher`). This is important since the debugger interferes with BugSplat's exception handling.
8. Click a button:

| Button | What it does |
| --- | --- |
| **Crash** | Throws an unhandled exception on the UI thread. WinUI fail-fasts the app, and BugSplat captures a minidump through Windows Error Reporting. |
| **Non-Crash Error** | Catches an exception and reports it with `BugSplat.Post` from inside the `catch`. The app keeps running. |
| **User Feedback** | Sends a note with `BugSplat.PostFeedback` and shows the resulting report id and a link to it. |
| **Hang** | Freezes the UI thread until BugSplat's hang detection reports the hang (within about 10 seconds) and ends the app. |
| **Mixed-Mode Crash** | Calls into C++ code that crashes, so the report has one call stack that crosses from C# into C++. |

9. Navigate to the BugSplat [Dashboard](https://app.bugsplat.com/v2/dashboard) and click the link in the ID column to view details about your crash, including the full symbolicated call stack and various crash metadata.

### Windows Error Reporting

WinUI turns an unhandled exception into a fail-fast that bypasses the application's exception handlers, so BugSplat captures a WinUI 3 app's crashes through its Windows Error Reporting helper, `BugSplatWer.dll`, which Windows loads only when its path is in the registry. From an elevated prompt, register the copy next to the sample's executable, then restart the sample:

```
reg add "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\RuntimeExceptionHelperModules" /v "<path to the sample's bin folder>\BugSplatWer.dll" /t REG_DWORD /d 0 /f
```

The app checks `BugSplat.IsWerEnabled` at startup and shows a warning until the entry exists.

### Shipping as MSIX

To package the app instead, remove `<WindowsPackageType>None</WindowsPackageType>` and `<WindowsAppSDKSelfContained>true</WindowsAppSDKSelfContained>` from the project file and add a Windows Application Packaging Project. The project declares the BugSplat runtime files as `Content` items, so they're included in the package unchanged; `BugSplatMonitor.exe` is packaged as a plain file, not listed as an `<Executable>` in the manifest.

Finally, explore how each button is implemented in the sample's source code (`MainWindow.xaml.cs` and `Crashers.cs`), and see [BugSplat for .NET](../../integrations/desktop/bugsplat-for-dot-net.md) to integrate BugSplat into your own application.
