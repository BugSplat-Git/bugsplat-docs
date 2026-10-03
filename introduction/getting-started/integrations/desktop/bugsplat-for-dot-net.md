---
description: >-
  Report crashes, hangs, and handled exceptions from .NET 10 applications on
  Windows with BugSplatDotNet.
---

# BugSplat for .NET

{% hint style="info" %}
This guide covers modern .NET (.NET 10). For .NET Framework 4.x applications, see [BugSplat for .NET Framework](windows-dot-net-framework.md). Both use the same `BugSplatDotNet` library from the same SDK download.
{% endhint %}

### Overview 👀

`BugSplatDotNet` adds crash reporting to .NET applications on Windows (x64). It's built on the BugSplat native SDK: every report is a minidump that BugSplat symbolicates from the symbols you upload, so call stacks show function names, file names, and line numbers for both managed (C#) and native (C++) frames, without shipping `.pdb` files with your application.

BugSplat reports:

| Event | How it's captured |
| --- | --- |
| Unhandled managed exceptions, on any thread | In-process exception filter → minidump |
| Hardware faults such as access violations, including ones in native code called through P/Invoke | In-process exception filter → minidump, with one mixed C#/C++ call stack |
| Handled exceptions you choose to report | `BugSplat.Post(exception)` → minidump; your app keeps running |
| Application hangs (apps with a window) | Hang detection → minidump of the hung process |
| Fail-fast crashes: heap corruption, `__fastfail`, `/GS` failures, crashes the .NET runtime ends with a fail-fast, and unhandled exceptions in WinUI 3 apps | Windows Error Reporting → `BugSplatWer.dll` → minidump (requires a registry entry; see [Windows Error Reporting](#windows-error-reporting)) |
| User feedback | `BugSplat.PostFeedback(...)` |

Crashes and handled exceptions alike are reported as minidumps with BugSplat's .NET crash type, so the managed frames are named from your symbols. Instructions for modifying the default crash dialog are on the [Windows Dialog Box](../../../../education/how-tos/customize-the-crash-dialog.md) page.

### Supported Versions

| Runtime | Support |
| --- | --- |
| .NET 10 and later | Supported. `BugSplatDotNet` targets `net10.0`, so your application must target .NET 10 or later to reference it. BugSplat symbolicates crashes from .NET 9 and later. |
| .NET Framework 4.x | Supported; see [BugSplat for .NET Framework](windows-dot-net-framework.md). |
| .NET 5 through 8, and .NET Core | **Not supported.** BugSplat can't symbolicate the managed frames of these runtimes and marks their crashes as coming from an unsupported version of .NET. Upgrade to .NET 10. |

`BugSplatDotNet` runs on **Windows x64** only: it loads the native `BugSplat.dll`, which ships for x64, so your application must run as a 64-bit x64 process. Linux and macOS aren't supported.

### Getting Started 🚦

To begin, [log in](https://app.bugsplat.com/cognito/login) and [download](https://app.bugsplat.com/browse/download_item.php?item=dotnet) the BugSplat SDK for .NET. Unzip it; the parts you need are:

| Folder | Contents |
| --- | --- |
| `BugSplat\dotnet\Release\net10.0` (and `Debug`) | `BugSplatDotNet.dll`, the library your application references, with its `.pdb` and `.xml` documentation |
| `BugSplat\x64\Release\bin` | The native runtime that ships next to your executable: `BugSplat.dll`, `BugSplatMonitor.exe`, `BugSplatRc.dll`, `BugSplatWer.dll` |
| `Tools` | `symbol-upload-windows.exe`, which the samples use to upload symbols |
| `Samples` | The sample applications, with a Visual Studio solution for each |

To get a feel for BugSplat before integrating it, try one of the samples:

* [MyDotNetCrasher](../../posting-a-test-crash/mydotnetcrasher/), a console app that triggers each kind of crash from the command line, including crashes in native C++ code called from C#.
* [MyDotNetWinUI3Crasher](../../posting-a-test-crash/mydotnetwinui3crasher/), a WinUI 3 app with a button for each kind of report.

{% hint style="info" %}
A NuGet package for `BugSplatDotNet` is coming. Until it's available, reference `BugSplatDotNet.dll` from the SDK download as described below.
{% endhint %}

### Integration 🏗️

1. **Reference `BugSplatDotNet.dll`** from `BugSplat\dotnet\Release\net10.0`:

   ```xml
   <ItemGroup>
     <Reference Include="BugSplatDotNet" HintPath="$(BugSplatDir)dotnet\Release\net10.0\BugSplatDotNet.dll" />
   </ItemGroup>
   ```

   where `$(BugSplatDir)` points at the SDK's `BugSplat\` folder.
2. **Build for x64.** The native runtime is x64 only, and an AnyCPU application runs as an ARM64 process on ARM64 Windows, where it can't load it. Set the platform target (or a `win-x64` runtime identifier):

   ```xml
   <PlatformTarget>x64</PlatformTarget>
   ```
3. **Ship the native runtime next to your executable.** `BugSplat.dll` starts `BugSplatMonitor.exe` from your application's directory to capture and upload crashes, so `BugSplat.dll`, `BugSplatMonitor.exe`, `BugSplatRc.dll`, and `BugSplatWer.dll` must be installed alongside your `.exe`. Declare them as `Content` items so `dotnet build` and `dotnet publish` copy them, and MSIX packaging includes them:

   ```xml
   <ItemGroup>
     <Content Include="$(BugSplatBin)BugSplat.dll" Link="BugSplat.dll" CopyToOutputDirectory="PreserveNewest" />
     <Content Include="$(BugSplatBin)BugSplatMonitor.exe" Link="BugSplatMonitor.exe" CopyToOutputDirectory="PreserveNewest" />
     <Content Include="$(BugSplatBin)BugSplatRc.dll" Link="BugSplatRc.dll" CopyToOutputDirectory="PreserveNewest" />
     <Content Include="$(BugSplatBin)BugSplatWer.dll" Link="BugSplatWer.dll" CopyToOutputDirectory="PreserveNewest" />
   </ItemGroup>
   ```

   where `$(BugSplatBin)` points at the SDK's `BugSplat\x64\Release\bin\`. Add the same four files to your installer. In an MSIX package, `BugSplatMonitor.exe` is a plain file; don't list it as an `<Executable>` in the manifest.

   {% hint style="warning" %}
   BugSplat's native runtime (`BugSplat.dll`, `BugSplatMonitor.exe`, and `BugSplatWer.dll`) depends on the **x64 Visual C++ 2015–2022 runtime**: `MSVCP140.dll`, `VCRUNTIME140.dll`, and `VCRUNTIME140_1.dll`. These DLLs are **not part of Windows** and are missing on machines where no application has installed the redistributable. The .NET runtime doesn't include them either, so without them your application runs normally but crash reporting fails. Make sure your installer either:

   * chains the x64 [Visual C++ Redistributable](https://learn.microsoft.com/en-us/cpp/windows/latest-supported-vc-redist) installer (`vc_redist.x64.exe`), or
   * copies `msvcp140.dll`, `vcruntime140.dll`, and `vcruntime140_1.dll` from the redistributable into your application folder alongside the BugSplat runtime files.
   {% endhint %}
4. **Initialize BugSplat once, as early as possible** (at the top of `Program.cs` or `Main`, or in your `App` constructor), and keep the instance for the life of the process:

   ```csharp
   using BugSplatDotNet;

   var bugsplat = new BugSplat("your-database", "YourApp", "1.0.0")
   {
       User = "fred",
       Email = "fred@example.com",
   };
   bugsplat.SetAttribute("channel", "beta");
   ```

   That's all it takes to report crashes and hangs. The database is created on the [Manage Database](https://app.bugsplat.com/v2/company/databases) page in Settings. No other handler is needed for unhandled exceptions, including exceptions on background threads and on a WinUI 3 UI thread.
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
6. **Register `BugSplatWer.dll` with Windows Error Reporting** from your installer, so fail-fast crashes are reported too. WinUI 3 applications need this for every crash. See [Windows Error Reporting](#windows-error-reporting) below.
7. **Upload symbols** for every build you ship, so call stacks show function names, file names, and line numbers. See [Symbols](#symbols) below.
8. **Test your integration** by forcing a crash with the application running outside the Visual Studio debugger (Ctrl+F5, or `dotnet run`); the debugger intercepts the exceptions BugSplat would report. Verify that symbols were uploaded on the [Versions](https://app.bugsplat.com/v2/versions) page and that the crash appears on the [Crashes](https://app.bugsplat.com/v2/crashes) page with a symbolicated call stack.

### Symbols

BugSplat symbolicates your crashes from the symbol files you upload, which is why your application doesn't need to ship its `.pdb` files. After each build, upload every `.exe`, `.dll`, and `.pdb` your application ships with [symbol-upload](../../../development/working-with-symbol-files/upload-symbols-with-symbol-upload.md), using the same database, application name, and version you pass to `new BugSplat(...)`. That includes the portable PDBs the .NET SDK produces for your managed code, `BugSplatDotNet.pdb`, and the native PDBs of any C++ libraries you call, so mixed C#/C++ call stacks are symbolicated on both sides.

symbol-upload authenticates with a Client ID and Client Secret, which you create on the [Integrations](https://app.bugsplat.com/v2/database/integrations#oauth) page. The samples run it after every build with a `Scripts\SymbolUpload.ps1` post-build step, which reads the credentials from `Scripts\env.ps1` or the `BUGSPLAT_CLIENT_ID` and `BUGSPLAT_CLIENT_SECRET` environment variables, fails the build if the upload fails, and is skipped when you build with `/p:BugSplatSymbolUpload=false`. Copy it into your own project, or run symbol-upload from your build pipeline.

### Windows Error Reporting

Some crashes bypass every in-process handler: Windows fail-fasts the process straight through Windows Error Reporting (WER). This happens for heap corruption (for example, a native library freeing the same memory twice), `__fastfail`, and `/GS` stack-cookie failures. On .NET it also happens for some access violations and stack overflows, which the runtime ends with a fail-fast, and for unhandled exceptions in a WinUI 3 application, which WinUI turns into a fail-fast. BugSplat captures these through its WER helper, `BugSplatWer.dll`, which Windows loads only when its full path is listed in the registry. Your installer should create the entry with administrator rights:

```
reg add "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\RuntimeExceptionHelperModules" /v "C:\Path\To\YourApp\BugSplatWer.dll" /t REG_DWORD /d 0 /f
```

`BugSplat.IsWerEnabled` tells you at run time whether the entry is in place. The [MyDotNetWinUI3Crasher](../../posting-a-test-crash/mydotnetwinui3crasher/) sample checks it at startup and warns when it's missing. See the [upgrade guide](cplusplus/bugsplat-for-windows-upgrade-guide.md#registry-changes) for more about the registry entry.

### Mixed C#/C++ Crashes

When managed code calls native code through P/Invoke and the native code crashes, BugSplat reports one call stack that interleaves the C# and C++ frames, symbolicated from both the managed and the native PDBs you upload. This covers native code that faults when called from C#, native code that calls back into C# code that throws, and crashes on native threads with no managed frames at all. The [MyDotNetCrasher](../../posting-a-test-crash/mydotnetcrasher/) sample's `native-*` and `cpp-throw` modes demonstrate each of these.

### Limitations

* **x64 Windows only.** Build your application for x64 (see step 2).
* **Fail-fast crashes need the WER registry entry.** Without it, heap corruption, `__fastfail`, `/GS` failures, runtime fail-fasts, and unhandled exceptions in WinUI 3 apps aren't reported.
* **Uncaught C++ exceptions** thrown from native code called through P/Invoke are reported from the point where the .NET runtime re-raises them, rather than from the C++ `throw`.

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

### Sample Apps

{% content-ref url="../../posting-a-test-crash/mydotnetcrasher/" %}
[MyDotNetCrasher (.NET)](../../posting-a-test-crash/mydotnetcrasher/)
{% endcontent-ref %}

{% content-ref url="../../posting-a-test-crash/mydotnetwinui3crasher/" %}
[MyDotNetWinUI3Crasher (.NET)](../../posting-a-test-crash/mydotnetwinui3crasher/)
{% endcontent-ref %}
