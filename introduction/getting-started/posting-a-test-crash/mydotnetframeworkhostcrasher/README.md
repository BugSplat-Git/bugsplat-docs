---
description: >-
  Testing crashes in .NET Framework code hosted by a native C++ application with
  the sample 'MyDotNetFrameworkHostCrasher'
---

# MyDotNetFrameworkHostCrasher (C++ hosting .NET Framework)

`MyDotNetFrameworkHostCrasher` is for applications that are native C++ programs which load the .NET Framework and call into C# code. The sample's C++ program initializes the [BugSplat native SDK](../../integrations/desktop/cplusplus/), starts the .NET Framework 4.7.2 runtime, and calls into a C# library that crashes. The report's call stack shows the C# frames on top of the C++ program's own frames.

To get started, download the BugSplat SDK for .NET by clicking [here](https://app.bugsplat.com/browse/download_item.php?item=dotnet), then unzip it.

1. Open `MyDotNetFrameworkHostCrasher.sln` with Visual Studio 2022+.
2. Define a value for `BUGSPLAT_DATABASE` in `Samples\MyDotNetFrameworkHostCrasher\MyDotNetFrameworkHostCrasher.h`.
3. Create a Client ID and Client Secret pair for your BugSplat database on the [Integrations](https://app.bugsplat.com/v2/settings/database/integrations) page.
4. Create a file `Samples\MyDotNetFrameworkHostCrasher\Scripts\env.ps1` and populate it with the following (being sure to substitute your `your-client-id` and `your-client-secret` values from the previous step):

```powershell
$BUGSPLAT_CLIENT_ID = "your-client-id"
$BUGSPLAT_CLIENT_SECRET = "your-client-secret"
```

5. Build the solution for **x64**. It builds the C# library (`MyDotNetFrameworkHostCrasherManaged`) and the C++ program, and a post-build step uploads their executables, DLLs, and `.pdb` files to BugSplat.
6. Run the program from a command prompt with one of the crash options below, outside of the Visual Studio debugger, for example:

```
MyDotNetFrameworkHostCrasher.exe /DeepThrow
```

| Option | What happens in the C# code |
| --- | --- |
| `/Throw` | Throws an unhandled exception. |
| `/DeepThrow` | Throws an unhandled exception four methods deep. |
| `/Thread` | Throws an unhandled exception on a worker thread. |
| `/AccessViolation` | Writes to an invalid address. |
| `/NativeCallback` | Calls back into a C++ function in the host program that crashes (C++ → C# → C++). |
| `/NoCrash` | Returns to the C++ program without crashing. |

Add `/Quiet` to skip the crash dialog. Run the program with no arguments to see every option.

7. When the crash dialog appears, click **Send Error Report**, then open the crash from the BugSplat [Dashboard](https://app.bugsplat.com/v2/dashboard) to see the combined C# and C++ call stack with file names and line numbers.

### How It Works

BugSplat is initialized in the C++ program, before the .NET Framework runtime starts, so its exception handling is in place when the C# code runs. The program calls `SetCrashType(8)`, which tells BugSplat to resolve the managed (C#) frames in its minidumps. To report crashes from your own C++ application that hosts .NET, follow [BugSplat for Windows (C++)](../../integrations/desktop/cplusplus/) and add that same call.

A stack overflow in the C# code isn't reported: the .NET Framework ends the process without running any exception handlers, and reports it only through its own Windows Error Reporting event.
