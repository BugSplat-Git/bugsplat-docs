---
description: >-
  Report crashes, hangs, and handled exceptions from .NET Framework and .NET 10
  applications on Windows with BugSplatDotNet.
---

# BugSplat for .NET

### Overview 👀

`BugSplatDotNet` adds crash reporting to .NET Framework 4.7.2+ and .NET 10+ applications on Windows. It's one library built for both runtimes from the same source, with the same API, and it's installed from one NuGet package, [`BugSplat`](https://www.nuget.org/packages/BugSplat). It's built on the BugSplat native SDK: every report is a minidump that BugSplat symbolicates from the symbols you upload, so call stacks show function names, file names, and line numbers for both managed (C#) and native (C++) frames, without shipping `.pdb` files with your application.

The package contains both builds, and NuGet picks the one for your target framework. Everything else in this guide (the native runtime you ship, initialization, handled exceptions, Windows Error Reporting, and symbols) is the same for both. Where a runtime behaves differently, a note says so.

BugSplat reports:

| Event | How it's captured |
| --- | --- |
| Unhandled managed exceptions, on any thread (including the WPF dispatcher) | In-process exception filter → minidump |
| Crashes in native code called from managed code (P/Invoke), including access violations | In-process exception filter → minidump, with one mixed C#/C++ call stack |
| Handled exceptions you choose to report | `BugSplat.Post(exception)` → minidump; your app keeps running |
| Application hangs (apps with a window) | Hang detection → minidump of the hung process |
| Fail-fast crashes: heap corruption, `__fastfail`, `/GS` failures, and on .NET 10 runtime fail-fasts and unhandled exceptions in WinUI 3 apps | Windows Error Reporting → `BugSplatWer.dll` → minidump (requires a registry entry; see [Windows Error Reporting](#windows-error-reporting)) |
| User feedback | `BugSplat.PostFeedback(...)` |

Crashes and handled exceptions alike are reported as minidumps with BugSplat's .NET crash type, so the managed frames are named from your symbols. Instructions for modifying the default crash dialog are on the [Windows Dialog Box](../../../../education/how-tos/customize-the-crash-dialog.md) page.

### Supported Versions

| Runtime | Support |
| --- | --- |
| .NET Framework 4.7.2 and later | Supported, with the `net472` build. |
| .NET 10 and later | Supported, with the `net10.0` build. |
| .NET 5 through 9, and .NET Core | Not supported by `BugSplatDotNet`. |

`BugSplatDotNet` runs on **Windows** only; Linux and macOS aren't supported. It loads the native `BugSplat.dll`, which must match the architecture your application runs as. The NuGet package includes the native runtime for **x64, x86, and ARM64** and copies the matching one; the SDK download includes x64 only.

{% hint style="info" %}
**.NET 10:** your application must target .NET 10 or later to reference the `net10.0` build. BugSplat's server can name the managed frames in crashes from .NET 9 and later, but not from .NET 5 through 8 or .NET Core, whose crashes it can't fully symbolicate.
{% endhint %}

### Getting Started 🚦

Install the [`BugSplat`](https://www.nuget.org/packages/BugSplat) NuGet package:

```
dotnet add package BugSplat
```

or search for **BugSplat** in Visual Studio's NuGet Package Manager. The same package works for .NET Framework 4.7.2+ and .NET 10+ projects.

To get a feel for BugSplat before integrating it, [log in](https://app.bugsplat.com/cognito/login), [download](https://app.bugsplat.com/browse/download_item.php?item=dotnet) the BugSplat SDK for .NET, and try one of the [samples](#sample-apps): [MyDotNetFrameworkWpfCrasher](../../posting-a-test-crash/mydotnetframeworkwpfcrasher/) for .NET Framework, or [MyDotNetWinUI3Crasher](../../posting-a-test-crash/mydotnetwinui3crasher/) and [MyDotNetCrasher](../../posting-a-test-crash/mydotnetcrasher/) for .NET 10. The download also includes `symbol-upload-windows.exe`, which the samples use to upload symbols.

### Integration 🏗️

1. **Add the [`BugSplat`](https://www.nuget.org/packages/BugSplat) NuGet package**, as shown in Getting Started above. It references `BugSplatDotNet.dll` for your target framework and brings BugSplat's native runtime with it.
2. **Ship the native runtime next to your executable.** `BugSplat.dll` starts `BugSplatMonitor.exe` from your application's directory to capture and upload crashes, so `BugSplat.dll`, `BugSplatMonitor.exe`, `BugSplatRc.dll`, and `BugSplatWer.dll` must be installed alongside your `.exe`. The package copies them to your build and `dotnet publish` output as content files; make sure your installer includes them.

   {% hint style="info" %}
   **Architecture:** the package copies the runtime for the architecture your application runs as. It uses your runtime identifier or `PlatformTarget` if you set one. Otherwise, a .NET 10 application gets the architecture of the .NET SDK that builds it. A .NET Framework AnyCPU application gets x86 with *Prefer 32-bit* and x64 without it. To choose explicitly, set `<BugSplatNativeArchitecture>` to `x64`, `x86`, or `arm64`.

   **Libraries:** the runtime is copied into executables and test projects. A library loaded by another application, such as a plug-in, can set `<BugSplatCopyNativeFiles>true</BugSplatCopyNativeFiles>`, and the files must then be deployed next to the host application's `.exe`.

   **MSIX:** `BugSplatMonitor.exe` is a plain file in the package; don't list it as an `<Executable>` in the manifest.
   {% endhint %}

   {% hint style="warning" %}
   BugSplat's native runtime (`BugSplat.dll`, `BugSplatMonitor.exe`, and `BugSplatWer.dll`) depends on the **Visual C++ 2015–2022 runtime** for your application's architecture: `MSVCP140.dll` and `VCRUNTIME140.dll`, plus `VCRUNTIME140_1.dll` on x64 and ARM64. These DLLs are **not part of Windows** and are missing on machines where no application has installed the redistributable. Neither the .NET Framework nor the .NET runtime includes them, so without them your application runs normally but crash reporting fails. Make sure your installer either:

   * chains the [Visual C++ Redistributable](https://learn.microsoft.com/en-us/cpp/windows/latest-supported-vc-redist) installer for your architecture (`vc_redist.x64.exe`, `vc_redist.x86.exe`, or `vc_redist.arm64.exe`), or
   * copies those DLLs from the redistributable into your application folder alongside the BugSplat runtime files.
   {% endhint %}
3. **Initialize BugSplat once, as early as possible** (at the top of `Main` or `Program.cs`, or in your `App` constructor), and keep the instance for the life of the process:

   ```csharp
   using BugSplatDotNet;

   var bugsplat = new BugSplat("your-database", "YourApp", "1.0.0")
   {
       User = "fred",
       Email = "fred@example.com",
   };
   bugsplat.SetAttribute("channel", "beta");
   ```

   That's all it takes to report crashes and hangs. The database is created on the [Manage Database](https://app.bugsplat.com/v2/company/databases) page in Settings. No other handler is needed for unhandled exceptions, including WPF dispatcher exceptions, exceptions on a WinUI 3 UI thread, and exceptions on background threads.
4. **Report handled exceptions** by calling `Post` from inside the `catch` block:

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
5. **Register `BugSplatWer.dll` with Windows Error Reporting** from your installer, so fail-fast crashes are reported too. WinUI 3 applications need it for every crash. See [Windows Error Reporting](#windows-error-reporting) below.
6. **Upload symbols** for every build you ship, so call stacks show function names, file names, and line numbers. See [Symbols](#symbols) below.
7. **Test your integration** by forcing a crash with the application running outside the Visual Studio debugger (Ctrl+F5, or `dotnet run`); the debugger intercepts the exceptions BugSplat would report. Verify that symbols were uploaded on the [Versions](https://app.bugsplat.com/v2/versions) page and that the crash appears on the [Crashes](https://app.bugsplat.com/v2/crashes) page with a symbolicated call stack.

### Using the SDK Download Instead of NuGet

If you can't use NuGet, reference `BugSplatDotNet.dll` from the [SDK download](https://app.bugsplat.com/browse/download_item.php?item=dotnet). Its native runtime is x64 only. Unzip it; the parts you need are:

| Folder | Contents |
| --- | --- |
| `BugSplat\dotnet\Release\net472` and `net10.0` (and `Debug`) | `BugSplatDotNet.dll`, the library your application references, with its `.pdb` and `.xml` documentation |
| `BugSplat\x64\Release\bin` | The native runtime that ships next to your executable: `BugSplat.dll`, `BugSplatMonitor.exe`, `BugSplatRc.dll`, `BugSplatWer.dll` |
| `Tools` | `symbol-upload-windows.exe`, which the samples use to upload symbols |
| `Samples` | The sample applications, with a Visual Studio solution for each |

These steps replace steps 1 and 2 of the Integration steps above; the rest are the same.

1. **Reference `BugSplatDotNet.dll`** from the folder for your target framework, `net472` for .NET Framework 4.7.2+ or `net10.0` for .NET 10+. In an SDK-style project:

   ```xml
   <ItemGroup>
     <Reference Include="BugSplatDotNet" HintPath="$(BugSplatDir)dotnet\Release\net10.0\BugSplatDotNet.dll" />
   </ItemGroup>
   ```

   where `$(BugSplatDir)` points at the SDK's `BugSplat\` folder.
2. **Build for x64:**

   ```xml
   <PlatformTarget>x64</PlatformTarget>
   ```

   {% hint style="info" %}
   **.NET Framework:** an executable defaults to AnyCPU with *Prefer 32-bit*, which runs as a 32-bit process and can't load the x64 runtime. Setting `PlatformTarget` to x64 fixes that.

   **.NET 10:** an AnyCPU application runs as an ARM64 process on ARM64 Windows, where it can't load the x64 runtime. Set `PlatformTarget` to x64, or use a `win-x64` runtime identifier.
   {% endhint %}
3. **Copy the native runtime next to your executable.** In an SDK-style project:

   ```xml
   <ItemGroup>
     <Content Include="$(BugSplatBin)BugSplat.dll" Link="BugSplat.dll" CopyToOutputDirectory="PreserveNewest" />
     <Content Include="$(BugSplatBin)BugSplatMonitor.exe" Link="BugSplatMonitor.exe" CopyToOutputDirectory="PreserveNewest" />
     <Content Include="$(BugSplatBin)BugSplatRc.dll" Link="BugSplatRc.dll" CopyToOutputDirectory="PreserveNewest" />
     <Content Include="$(BugSplatBin)BugSplatWer.dll" Link="BugSplatWer.dll" CopyToOutputDirectory="PreserveNewest" />
   </ItemGroup>
   ```

   where `$(BugSplatBin)` points at the SDK's `BugSplat\x64\Release\bin\`. `Content` items are copied by both `dotnet build` and `dotnet publish`. Add the same four files to your installer.

### Symbols

BugSplat symbolicates your crashes from the symbol files you upload, which is why your application doesn't need to ship its `.pdb` files. After each build, upload every `.exe`, `.dll`, and `.pdb` your application ships with [symbol-upload](../../../development/working-with-symbol-files/upload-symbols-with-symbol-upload.md), using the same database, application name, and version you pass to `new BugSplat(...)`. That includes `BugSplatDotNet.pdb`, BugSplat's native PDBs, and the native PDBs of any C++ libraries you call, so mixed C#/C++ call stacks are symbolicated on both sides.

The NuGet package copies BugSplat's native PDBs to your build output, but NuGet doesn't copy `BugSplatDotNet.pdb` from a package unless you ask it to:

```xml
<CopyDebugSymbolFilesFromPackages>true</CopyDebugSymbolFilesFromPackages>
```

{% hint style="info" %}
**.NET Framework:** emit full Windows PDBs, the format BugSplat's symbol upload expects:

```xml
<DebugType>full</DebugType>
```

**.NET 10:** the portable PDBs the .NET SDK produces by default work as they are.
{% endhint %}

symbol-upload authenticates with a Client ID and Client Secret, which you create on the [Integrations](https://app.bugsplat.com/v2/database/integrations#oauth) page. The samples run it after every build with a `Scripts\SymbolUpload.ps1` post-build step, which reads the credentials from `Scripts\env.ps1` or the `BUGSPLAT_CLIENT_ID` and `BUGSPLAT_CLIENT_SECRET` environment variables. Building with `/p:BugSplatSymbolUpload=false` skips it, and in the .NET 10 samples a failed upload fails the build. Copy the script into your own project, or run symbol-upload from your build pipeline.

### Windows Error Reporting

Some crashes bypass every in-process handler: Windows fail-fasts the process straight through Windows Error Reporting (WER). This happens for heap corruption (for example, a native library freeing the same memory twice), `__fastfail`, and `/GS` stack-cookie failures. BugSplat captures these through its WER helper, `BugSplatWer.dll`, which Windows loads only when its full path is listed in the registry. Your installer should create the entry with administrator rights:

```
reg add "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\RuntimeExceptionHelperModules" /v "C:\Path\To\YourApp\BugSplatWer.dll" /t REG_DWORD /d 0 /f
```

`BugSplat.IsWerEnabled` tells you at run time whether the entry is in place. See the [upgrade guide](cplusplus/bugsplat-for-windows-upgrade-guide.md#registry-changes) for more about the registry entry.

{% hint style="info" %}
**.NET Framework:** unhandled managed exceptions and access violations reach BugSplat's in-process filter and don't need the entry.

**.NET 10:** the runtime ends some access violations and stack overflows with a fail-fast, and WinUI turns every unhandled exception in a WinUI 3 app into a fail-fast, so only the WER helper captures them. A WinUI 3 app's crashes aren't reported without the entry; the [MyDotNetWinUI3Crasher](../../posting-a-test-crash/mydotnetwinui3crasher/) sample checks `IsWerEnabled` at startup and warns when it's missing.
{% endhint %}

### Mixed C#/C++ Crashes

When managed code calls native code through P/Invoke and the native code crashes, BugSplat reports one call stack that interleaves the C# and C++ frames, symbolicated from both the managed and the native PDBs you upload. This covers native code that faults when called from C#, native code that calls back into C# code that throws, and crashes on native threads with no managed frames at all. The [MyDotNetCrasher](../../posting-a-test-crash/mydotnetcrasher/) sample's `native-*` and `cpp-throw` modes demonstrate each of these, and the Mixed-Mode buttons of the WPF and WinUI 3 samples show the simplest case.

### Limitations

* **Windows only.** The NuGet package supports x64, x86, and ARM64; the SDK download supports x64 only.
* **Start your application with its `.exe`.** `dotnet YourApp.dll` runs your application inside `dotnet.exe`, and BugSplat looks for `BugSplatMonitor.exe` next to `dotnet.exe` instead of your application.
* **Fail-fast crashes need the WER registry entry.** Without it, heap corruption, `__fastfail`, `/GS` failures, and on .NET 10 runtime fail-fasts and WinUI 3 crashes aren't reported.
* **Unobserved task exceptions aren't reported.** They don't crash the process, so there's no crash to capture. Observe your tasks and report failures with `Post` from a `catch`.
* **.NET Framework: stack overflows in managed code aren't reported.** The .NET Framework ends the process on a stack overflow without running any in-process handler, and reports it through its own `CLR20r3` WER event, which doesn't call `BugSplatWer.dll` even when it's registered.
* **.NET 10: uncaught C++ exceptions** thrown from native code called through P/Invoke are reported from the point where the .NET runtime re-raises them, rather than from the C++ `throw`.

### Native Applications That Host .NET

If your application is a native C++ program that loads the .NET Framework and calls into managed code, integrate the native SDK, [BugSplat for Windows (C++)](cplusplus/), in the host program instead of `BugSplatDotNet`, and call `SetCrashType(8)` so BugSplat resolves the managed frames in its minidumps. Crashes in the managed code are captured by the native SDK's handlers, and the call stack shows the managed frames on top of your native ones. The [MyDotNetFrameworkHostCrasher](../../posting-a-test-crash/mydotnetframeworkhostcrasher/) sample demonstrates the setup.

### API Reference 📖

| Member | Description |
| --- | --- |
| `BugSplat(string database, string application, string version)` | Initializes crash reporting. Create one instance, early, and keep it for the life of the process. |
| `bool Post(Exception exception)` | Reports a handled exception as a minidump and uploads it. Call it inside the `catch` block. Returns whether the report was uploaded. |
| `FeedbackResult PostFeedback(string title, string? description = null, string[]? attachments = null)` | Posts user feedback. The result's `Success`, `CrashId`, and `InfoUrl` describe the report. It blocks while the report is uploaded, so call it off the UI thread. |
| `string User`, `Email`, `Key`, `Description`, `Notes` | Default values stamped on every report. |
| `void SetAttribute(string name, string value)` | Adds a custom attribute to every report. |
| `bool AddAttachment(string filePath)` | Attaches a file to every report. |
| `bool QuietMode` | Set to `true` to suppress the crash dialog; reports are still uploaded. |
| `MiniDumpType MiniDumpType` | The minidump type for crash reports and `Post`. The default, `MiniDumpType.Normal`, is enough for BugSplat to name the managed frames. Larger types, such as `WithPrivateReadWriteMemory` or `WithFullMemory`, are written only when [full memory dumps](cplusplus/full-memory-dumps.md) are enabled for your database; otherwise BugSplat writes a normal minidump. |
| `bool IsWerEnabled` | Whether `BugSplatWer.dll` is registered with Windows Error Reporting. |
| `bool IsInitialized` | Whether crash reporting is installed for this process. |

### Upgrading From the Legacy .NET Framework SDK

The previous .NET Framework SDK (`BugSplat.CrashReporter`) has been replaced by `BugSplatDotNet`. To upgrade:

| Legacy SDK | BugSplatDotNet |
| --- | --- |
| `BugSplat.CrashReporter.Init(database, app, version)` | `new BugSplat(database, app, version)` |
| Subscribing `AppDomainUnhandledExceptionHandler`, `DispatcherUnhandledExceptionHandler`, and `TaskSchedulerUnobservedTaskExceptionHandler` | Nothing: unhandled exceptions are captured by the constructor |
| `CrashReporter.createReport(exception)` for handled exceptions | `bugsplat.Post(exception)`, inside the `catch` |
| Referencing `BugSplatDotNet.dll` from the SDK download | The [`BugSplat`](https://www.nuget.org/packages/BugSplat) NuGet package |
| Shipping `BsSndRpt.exe`, `BugSplatDotNet.dll`, and `BugSplatRc.dll` | Shipping `BugSplatDotNet.dll` plus `BugSplat.dll`, `BugSplatMonitor.exe`, `BugSplatRc.dll`, and `BugSplatWer.dll`, which the NuGet package copies to your output |
| Any CPU | Any architecture with the NuGet package, which copies the matching native runtime; x64 with the SDK download |
| SendPdbs | [symbol-upload](../../../development/working-with-symbol-files/upload-symbols-with-symbol-upload.md) |

### Sample Apps

| Sample | Runtime | What it shows |
| --- | --- | --- |
| [MyDotNetFrameworkWpfCrasher](../../posting-a-test-crash/mydotnetframeworkwpfcrasher/) | .NET Framework 4.7.2 | A WPF app with a button for each kind of report |
| [MyDotNetFrameworkHostCrasher](../../posting-a-test-crash/mydotnetframeworkhostcrasher/) | .NET Framework 4.7.2, hosted by C++ | A native C++ program that crashes in the C# code it calls |
| [MyDotNetWinUI3Crasher](../../posting-a-test-crash/mydotnetwinui3crasher/) | .NET 10 | A WinUI 3 app with a button for each kind of report |
| [MyDotNetCrasher](../../posting-a-test-crash/mydotnetcrasher/) | .NET 10 | A console app with a mode for every crash, including mixed C#/C++ crashes |
