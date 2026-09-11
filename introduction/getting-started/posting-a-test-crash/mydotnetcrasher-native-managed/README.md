---
description: >-
  Testing mixed-mode C#/C++ and managed .NET crashes with the sample application
  'MyDotNetCrasher'
---

# MyDotNetCrasher (.NET)

`MyDotNetCrasher` is a headless .NET 10 console sample that reports crashes with the
[BugSplat for Windows (.NET)](../../integrations/desktop/bugsplat-for-windows-dot-net.md)
(`BugSplatDotNet`) SDK. It exercises **both** capture layers — the managed reporter for C#
exceptions and the native handler for hard faults — including a full set of **mixed-mode C#/C++**
crashes that cross the managed/native boundary.

{% hint style="info" %}
This is the native+managed sample that ships with the `BugSplatDotNet` SDK in
[bugsplat-windows](https://github.com/BugSplat-Git/bugsplat-windows). For the standalone managed
transport sample, see [MyDotnetCrasher (.NET)](../my-dotnet-crasher/README.md).
{% endhint %}

### Prerequisites 🚦

* [.NET 10 SDK](https://dotnet.microsoft.com/download)
* Visual Studio 2022+ (to build the native `MyDotNetCrasherNative.dll` for the mixed-mode modes)
* A [BugSplat account](https://app.bugsplat.com/v2/sign-up) and a [database](../../create-a-new-database-in-bugsplat.md)
* Windows, x64

### 1. Get the sample

Clone [bugsplat-windows](https://github.com/BugSplat-Git/bugsplat-windows) and open
`MyDotNetCrasher.sln`, or run from `Samples/MyDotNetCrasher`.

### 2. Configure your database

Set your database at the top of `Program.cs`:

```csharp
var bugsplat = new BugSplatDotNet.BugSplat("your-database", "MyDotNetCrasher", "1.0.0");
```

Upload the app's PDBs (and `MyDotNetCrasherNative.pdb`) with
[symbol-upload](../../../development/working-with-symbol-files/) so crashes are symbolicated.

### 3. Build the native crash surface (for mixed-mode)

The mixed-mode modes P/Invoke `MyDotNetCrasherNative.dll`. Build it first (a .NET SDK project
can't build a `.vcxproj`), then the console project copies it next to the exe:

```
msbuild Samples\MyDotNetCrasherNative\MyDotNetCrasherNative.vcxproj -p:Configuration=Release -p:Platform=x64
```

### 4. Post a test crash

Run **outside the debugger** with a mode:

```
dotnet run -- managed         # unhandled C# exception (managed reporter posts the C# stack)
dotnet run -- native          # hardware access violation (native handler captures a minidump)
dotnet run -- feedback        # non-crashing user feedback (returns a report id)
```

**Mixed-mode C#/C++** — the crash stack crosses the managed/native boundary and is symbolicated
as one unified stack:

```
dotnet run -- native-av             # managed -> native access violation
dotnet run -- native-deep           # native frames beneath the managed transition
dotnet run -- native-callback       # managed -> native -> managed callback that throws
dotnet run -- cpp-throw             # uncaught C++ exception
dotnet run -- native-so             # cross-boundary stack overflow
dotnet run -- native-thread         # native background-thread fault
```

**WER-class** modes (fail-fasts captured only when WER is configured — see the
[integration guide](../../integrations/desktop/bugsplat-for-windows-dot-net.md)):

```
dotnet run -- native-fastfail        # __fastfail
dotnet run -- native-overrun         # /GS stack-cookie smash
dotnet run -- native-double-delete   # heap-metadata corruption
```

### 5. View the crash ✅

Open the [Dashboard](https://app.bugsplat.com/v2/dashboard), select your database, and click a
report's **ID**. Mixed-mode crashes show a single call stack with both C# frames (with file/line
from the portable PDB) and native C++ frames — for example
`MyDotNetCrasherNative!bscrash_fastfail` calling into `Program.<Main>$`.
