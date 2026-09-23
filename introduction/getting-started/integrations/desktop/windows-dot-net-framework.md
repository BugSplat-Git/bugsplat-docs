---
description: >-
  Report crashes, hangs, and handled exceptions from .NET Framework applications
  on Windows with BugSplatDotNet.
---

# BugSplat for .NET Framework

### Overview 👀

`BugSplatDotNet` adds crash reporting to .NET Framework 4.7.2+ applications on Windows (x64). It's built on the BugSplat native SDK: every report is a minidump that BugSplat symbolicates from the symbols you upload, so call stacks show function names, file names, and line numbers for both managed (C#) and native (C++) frames, without shipping `.pdb` files with your application.

BugSplat reports:

| Event | How it's captured |
| --- | --- |
| Unhandled managed exceptions, on any thread (including the WPF dispatcher) | In-process exception filter → minidump |
| Crashes in native code called from managed code (P/Invoke), including access violations | In-process exception filter → minidump, with one mixed C#/C++ call stack |
| Handled exceptions you choose to report | `BugSplat.Post(exception)` → minidump; your app keeps running |
| Application hangs | Hang detection → minidump of the hung process |
| Fail-fast crashes in native code (heap corruption, `__fastfail`, `/GS` failures) | Windows Error Reporting → `BugSplatWer.dll` → minidump (requires a registry entry; see [Windows Error Reporting](#windows-error-reporting)) |
| User feedback | `BugSplat.PostFeedback(...)` |

Instructions for modifying the default crash dialog are on the [Windows Dialog Box](../../../../education/how-tos/customize-the-crash-dialog.md) page.

### Getting Started 🚦

To begin, [log in](https://app.bugsplat.com/cognito/login) and [download](https://app.bugsplat.com/browse/download_item.php?item=dotnet) the BugSplat SDK for .NET. Unzip it; the parts you need are:

| Folder | Contents |
| --- | --- |
| `BugSplat\dotnet\Release\net472` (and `Debug`) | `BugSplatDotNet.dll`, the library your application references, with its `.pdb` and `.xml` documentation |
| `BugSplat\x64\Release\bin` | The native runtime that ships next to your executable: `BugSplat.dll`, `BugSplatMonitor.exe`, `BugSplatRc.dll`, `BugSplatWer.dll` |
| `Samples` | The sample applications, with a Visual Studio solution for each |

To get a feel for BugSplat before integrating it, try the [MyDotNetFrameworkWpfCrasher](../../posting-a-test-crash/mydotnetframeworkwpfcrasher/) sample, a WPF app with a button for each kind of report. If your application is a native C++ program that hosts the .NET Framework, see [MyDotNetFrameworkHostCrasher](../../posting-a-test-crash/mydotnetframeworkhostcrasher/) instead.

### Integration 🏗️

1. **Reference `BugSplatDotNet.dll`** from `BugSplat\dotnet\Release\net472`.
2. **Build for x64.** The native runtime is x64 only, and a .NET Framework executable defaults to AnyCPU with *Prefer 32-bit*, which runs as a 32-bit process and can't load it. Set the platform target to x64:

   ```xml
   <PlatformTarget>x64</PlatformTarget>
   ```
3. **Ship the native runtime next to your executable.** `BugSplat.dll` starts `BugSplatMonitor.exe` from your application's directory to capture and upload crashes, so `BugSplat.dll`, `BugSplatMonitor.exe`, `BugSplatRc.dll`, and `BugSplatWer.dll` must be installed alongside your `.exe`. In an SDK-style project, copying them from the SDK looks like this:

   ```xml
   <ItemGroup>
     <Content Include="$(BugSplatBin)BugSplat.dll" Link="BugSplat.dll" CopyToOutputDirectory="PreserveNewest" />
     <Content Include="$(BugSplatBin)BugSplatMonitor.exe" Link="BugSplatMonitor.exe" CopyToOutputDirectory="PreserveNewest" />
     <Content Include="$(BugSplatBin)BugSplatRc.dll" Link="BugSplatRc.dll" CopyToOutputDirectory="PreserveNewest" />
     <Content Include="$(BugSplatBin)BugSplatWer.dll" Link="BugSplatWer.dll" CopyToOutputDirectory="PreserveNewest" />
   </ItemGroup>
   ```

   where `$(BugSplatBin)` points at the SDK's `BugSplat\x64\Release\bin\`. Add the same four files to your installer.
4. **Initialize BugSplat once, as early as possible** (for example in your `App` constructor or at the top of `Main`), and keep the instance for the life of the process:

   ```csharp
   using BugSplatDotNet;

   var bugsplat = new BugSplat("your-database", "YourApp", "1.0.0")
   {
       User = "fred",
       Email = "fred@example.com",
   };
   bugsplat.SetAttribute("channel", "beta");
   ```

   That's all it takes to report crashes and hangs. The database is created on the [Manage Database](https://app.bugsplat.com/v2/company/databases) page in Settings. No other handler is needed for unhandled exceptions, including WPF dispatcher exceptions and exceptions on background threads.
5. **Report handled exceptions** by calling `Post` from inside the `catch` block:

   ```csharp
   try
   {
       LoadDocument(path);
   }
   catch (Exception ex)
   {
       bugsplat.Post(ex);
   }
   ```

   `Post` writes a minidump of the exception being handled and uploads it; your application keeps running. Call it inside the `catch`, while the frames of the code that threw are still on the stack, so the report shows where the exception came from. Called anywhere else, there is no exception in flight and `Post` returns `false` without reporting anything. It blocks while the report is written and uploaded.
6. **Upload symbols** for every build you ship, so call stacks show function names, file names, and line numbers. See [Symbols](#symbols) below.
7. **Test your integration** by forcing a crash with the application running outside the Visual Studio debugger (Ctrl+F5); the debugger intercepts the exceptions BugSplat would report. Verify that symbols were uploaded on the [Versions](https://app.bugsplat.com/v2/versions) page and that the crash appears on the [Crashes](https://app.bugsplat.com/v2/crashes) page with a symbolicated call stack.

### Symbols

BugSplat symbolicates your crashes from the symbol files you upload, which is why your application doesn't need to ship its `.pdb` files. Emit full Windows PDBs, the format BugSplat's symbol upload expects:

```xml
<DebugType>full</DebugType>
```

Then upload your executable, DLLs, and their `.pdb` files after each build with [symbol-upload](../../../development/working-with-symbol-files/upload-symbols-with-symbol-upload.md), using the same database, application name, and version you pass to `new BugSplat(...)`. The [MyDotNetFrameworkWpfCrasher](../../posting-a-test-crash/mydotnetframeworkwpfcrasher/) sample does this in a post-build step with its `Scripts\SymbolUpload.ps1`.

### Windows Error Reporting

Some crashes bypass every in-process handler: Windows fail-fasts the process straight through Windows Error Reporting (WER). This happens for heap corruption (for example, a native library freeing the same memory twice), `__fastfail`, and `/GS` stack-cookie failures. BugSplat captures these through its WER helper, `BugSplatWer.dll`, which Windows loads only when its full path is listed in the registry. Your installer should create the entry with administrator rights:

```
reg add "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\RuntimeExceptionHelperModules" /v "C:\Path\To\YourApp\BugSplatWer.dll" /t REG_DWORD /d 0 /f
```

`BugSplat.IsWerEnabled` tells you at run time whether the entry is in place. Unhandled managed exceptions and access violations don't need it: on .NET Framework they reach BugSplat's in-process filter. See the [upgrade guide](cplusplus/bugsplat-for-windows-upgrade-guide.md#registry-changes) for more about the registry entry.

### Limitations

* **Stack overflows in managed code aren't reported.** The .NET Framework ends the process on a stack overflow without running any in-process handler, and reports it through its own WER event, which doesn't call `BugSplatWer.dll`.
* **Unobserved task exceptions aren't reported.** They don't crash the process on .NET Framework 4.5 and later, so there's no crash to capture. Observe your tasks and report failures with `Post` from a `catch`.
* **x64 only.** Build your application for x64 (see step 2).

### Native Applications That Host .NET

If your application is a native C++ program that loads the .NET Framework and calls into managed code, integrate the native SDK, [BugSplat for Windows (C++)](cplusplus/), in the host program instead of `BugSplatDotNet`, and call `SetCrashType(8)` so BugSplat resolves the managed frames in its minidumps. Crashes in the managed code are captured by the native SDK's handlers, and the call stack shows the managed frames on top of your native ones. The [MyDotNetFrameworkHostCrasher](../../posting-a-test-crash/mydotnetframeworkhostcrasher/) sample demonstrates the setup.

### API Reference 📖

| Member | Description |
| --- | --- |
| `BugSplat(string database, string application, string version)` | Initializes crash reporting. Create one instance, early, and keep it for the life of the process. |
| `bool Post(Exception exception)` | Reports a handled exception as a minidump and uploads it. Call it inside the `catch` block. Returns whether the report was uploaded. |
| `FeedbackResult PostFeedback(string title, string description = null, string[] attachments = null)` | Posts user feedback. The result's `Success`, `CrashId`, and `InfoUrl` describe the report. |
| `string User`, `Email`, `Key`, `Description`, `Notes` | Default values stamped on every report. |
| `void SetAttribute(string name, string value)` | Adds a custom attribute to every report. |
| `bool AddAttachment(string filePath)` | Attaches a file to every report. |
| `bool QuietMode` | Suppresses the crash dialog; reports are still uploaded. |
| `MiniDumpType MiniDumpType` | The minidump type for crash reports and `Post`. The default, `MiniDumpType.Normal`, is enough for BugSplat to name the managed frames. |
| `bool IsWerEnabled` | Whether `BugSplatWer.dll` is registered with Windows Error Reporting. |
| `bool IsInitialized` | Whether crash reporting is installed for this process. |

### Upgrading From the Legacy .NET Framework SDK

The previous .NET Framework SDK (`BugSplat.CrashReporter`) has been replaced by `BugSplatDotNet`. To upgrade:

| Legacy SDK | BugSplatDotNet |
| --- | --- |
| `BugSplat.CrashReporter.Init(database, app, version)` | `new BugSplat(database, app, version)` |
| Subscribing `AppDomainUnhandledExceptionHandler`, `DispatcherUnhandledExceptionHandler`, and `TaskSchedulerUnobservedTaskExceptionHandler` | Nothing: unhandled exceptions are captured by the constructor |
| `CrashReporter.createReport(exception)` for handled exceptions | `bugsplat.Post(exception)`, inside the `catch` |
| Shipping `BsSndRpt.exe`, `BugSplatDotNet.dll`, and `BugSplatRc.dll` | Shipping `BugSplatDotNet.dll` plus `BugSplat.dll`, `BugSplatMonitor.exe`, `BugSplatRc.dll`, and `BugSplatWer.dll` |
| Any CPU | x64 |
| SendPdbs | [symbol-upload](../../../development/working-with-symbol-files/upload-symbols-with-symbol-upload.md) |
