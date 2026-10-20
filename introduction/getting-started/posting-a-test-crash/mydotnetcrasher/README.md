---
description: >-
  Testing .NET crashes, handled exceptions, and mixed C#/C++ crashes with the
  sample console application 'MyDotNetCrasher'
---

# MyDotNetCrasher (.NET)

Before you enable BugSplat in your .NET application, you may want to take a moment to experiment with our `MyDotNetCrasher` sample, a .NET 10 console app that triggers each kind of crash [BugSplat for .NET](../../integrations/desktop/bugsplat-for-dot-net.md) reports, chosen from the command line. Its mixed-mode crashes call into a small C++ library, `MyDotNetCrasherNative`, so you can see BugSplat report one call stack that crosses from C# into C++.

To get started, clone [my-dotnet-crasher](https://github.com/BugSplat-Git/my-dotnet-crasher), where the sample installs BugSplat from the [`BugSplat`](https://www.nuget.org/packages/BugSplat) NuGet package. You can also [download the BugSplat SDK for .NET](https://app.bugsplat.com/browse/download_item.php?item=dotnet) and unzip it. You'll need the [.NET 10 SDK](https://dotnet.microsoft.com/download/dotnet/10.0) and Visual Studio with the C++ desktop workload, which builds `MyDotNetCrasherNative`; the SDK download's copy also needs the `v143` build tools.

1. Open `MyDotNetCrasher.sln` with Visual Studio.
2. Set your database at the top of `Samples\MyDotNetCrasher\Program.cs`, and optionally the application name and version. Keep the line's shape: the symbol upload script reads the three values from it.

   ```csharp
   var bugsplat = new BugSplatDotNet.BugSplat("your-database", "MyDotNetCrasher", "1.0.0")
   {
       User = "sample-user",
       Email = "sample-user@example.com",
       Description = $"MyDotNetCrasher {mode} crash",
   };
   ```
3. Create a Client ID and Client Secret pair for your BugSplat database on the [Integrations](https://app.bugsplat.com/v2/database/integrations#oauth) page.
4. Create a file `Samples\MyDotNetCrasher\Scripts\env.ps1` and populate it with the following (being sure to substitute your `your-client-id` and `your-client-secret` values from the previous step), or set the `BUGSPLAT_CLIENT_ID` and `BUGSPLAT_CLIENT_SECRET` environment variables instead:

```powershell
$BUGSPLAT_CLIENT_ID = "your-client-id"
$BUGSPLAT_CLIENT_SECRET = "your-client-secret"
```

5. Build the solution (`Debug`, `x64`). It builds `MyDotNetCrasherNative` first, then the console app, which copies the BugSplat runtime and `MyDotNetCrasherNative.dll` next to its executable. A post-build step uploads the sample's executable, DLLs, and `.pdb` files (managed and native) to BugSplat with `Scripts\SymbolUpload.ps1`, so its crashes show file names and line numbers. If the upload fails, so does the build; to build without uploading, pass `/p:BugSplatSymbolUpload=false`.
6. From `Samples\MyDotNetCrasher`, run the sample with a crash mode:

```
dotnet run -- managed
```

The sample sets `QuietMode`, so it uploads each report without showing the crash dialog. Run it from a terminal rather than under the Visual Studio debugger, which intercepts the exceptions BugSplat would report.

7. Navigate to the BugSplat [Dashboard](https://app.bugsplat.com/v2/dashboard) and click the link in the ID column to view details about your crash, including the full symbolicated call stack and various crash metadata.

### Crash Modes

Every crash runs a few frames deep, through `SampleStackFrame0`, `SampleStackFrame1`, and `SampleStackFrame2`, and then happens in a method named after it, such as `ThrowUnhandledManagedException` or `CrashNativeAccessViolation`, so each report shows a real call stack whose top frame says what happened.

| Mode | What it does |
| --- | --- |
| `managed` | Throws an unhandled C# exception from `ThrowUnhandledManagedException`. BugSplat captures a minidump. |
| `handled` | Catches an exception and reports it with `BugSplat.Post` from inside the `catch`. The app keeps running. |
| `native` | Writes to an invalid address from C# (`CrashAccessViolation`), an access violation the runtime never turns into a managed exception. |
| `feedback` | Sends a note with `BugSplat.PostFeedback` and prints the resulting report id and link. |
| `deep`, `generic`, `lambda`, `async` | Crash several managed frames deep, in a generic method, in a LINQ lambda, and in an `async` method, to show how BugSplat names each kind of frame. |

#### Mixed C#/C++ crashes

These call into `MyDotNetCrasherNative.dll`, so the report has one call stack with both C# and C++ frames:

| Mode | What it does |
| --- | --- |
| `native-av` | C# calls C++ code that causes an access violation (`CrashNativeAccessViolation`). |
| `native-deep` | The access violation happens several C++ frames below the C# caller (`CrashNativeDeep`). |
| `native-callback` | C# calls C++, which calls back into C# code that throws (`CrashNativeCallback`). |
| `cpp-throw` | C++ code throws an exception nothing catches (`CrashCppThrow`). |
| `native-so` | C# and C++ call each other until the stack overflows (`CrashNativeStackOverflow`). |
| `native-thread` | A C++ background thread with no C# frames crashes (`CrashNativeThread`). |
| `native-fastfail` | C++ code calls `__fastfail` (`CrashNativeFastFail`). Requires Windows Error Reporting; see below. |
| `native-overrun` | C++ code overruns a stack buffer and fails its `/GS` check (`CrashNativeStackOverrun`). Requires Windows Error Reporting. |
| `native-double-delete` | C++ code frees the same memory twice (`CrashNativeDoubleDelete`). Requires Windows Error Reporting, and a `Release` build: in `Debug` the debug C runtime reports it first. |

### Fail-Fast Crashes and Windows Error Reporting

The `native-fastfail`, `native-overrun`, and `native-double-delete` modes fail-fast the process straight through Windows Error Reporting, bypassing the application's exception handlers, so BugSplat captures them through its Windows Error Reporting helper, `BugSplatWer.dll`, which Windows loads only when its path is in the registry. On .NET, the runtime can also end some access violations and stack overflows with a fail-fast, which only this helper captures. From an elevated prompt, register the copy next to the sample's executable:

```
reg add "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\RuntimeExceptionHelperModules" /v "<path to the sample's bin folder>\BugSplatWer.dll" /t REG_DWORD /d 0 /f
```

The other modes are captured by BugSplat's application exception handler. Your own application should always register `BugSplatWer.dll`, because crashes like these can only be captured through it.

Finally, explore how each mode is implemented in the sample's `Program.cs`, and see [BugSplat for .NET](../../integrations/desktop/bugsplat-for-dot-net.md) to integrate BugSplat into your own application.
