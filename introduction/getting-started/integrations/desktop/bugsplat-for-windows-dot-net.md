---
description: >-
  Add crash, hang, and user-feedback reporting to a modern .NET (net8.0 / net10.0)
  Windows app with the BugSplatDotNet SDK
---

# BugSplat for Windows (.NET)

{% hint style="info" %}
This is the SDK for **modern .NET on Windows** (.NET 8 / .NET 10). For the classic .NET
Framework SDK see [.NET Framework](windows-dot-net-framework.md); for the cross-platform managed
transport see [.NET Standard](dot-net-standard.md).
{% endhint %}

### Overview 👀

`BugSplatDotNet` gives a .NET Windows application the same crash coverage as the native
[BugSplat for Windows (C++)](cplusplus/README.md) SDK, plus managed exception reporting. It
combines two complementary layers behind one `BugSplat` class:

* **Native (`BugSplat.dll`)** — installs the native unhandled-exception filter and the WER
  handler and spawns `BugSplatMonitor.exe` on a genuine hardware/native fault (access violation,
  fail-fast, stack overflow, corrupted heap, or a crash inside P/Invoke). The CLR never surfaces
  these as managed exceptions, so a managed-only reporter cannot see them.
* **Managed (`BugSplatDotNetStandard`)** — posts unhandled managed `Exception`s (with their C#
  stack traces) that the native filter never receives.

Because the two layers cover different crash classes, a mixed **C#/C++** crash — managed code
that P/Invokes into native code that faults — is captured as a single, unified call stack that
crosses the managed/native boundary.

### Getting Started 🚦

The SDK and three sample apps live in the
[bugsplat-windows](https://github.com/BugSplat-Git/bugsplat-windows) repository under
`BugSplatDotNet/` and `Samples/`. Reference `BugSplatDotNet.csproj` from your app (or copy the
files listed in `BugSplatDotNetFiles.txt`). The SDK is **x64** — it links the native
`BugSplat.dll`, which ships x64.

### 1. Initialize

Create one `BugSplat` instance at startup and wire the managed handlers:

```csharp
using BugSplatDotNet;

var bugsplat = new BugSplat("your-database", "YourApplication", "1.0.0");
bugsplat.HandleApplicationExceptions();   // AppDomain + unobserved tasks
```

In a WinUI 3 / XAML app, also forward UI-thread exceptions:

```csharp
UnhandledException += (s, e) => { App.BugSplat.Post(e.Exception).GetAwaiter().GetResult(); };
```

### 2. Ship the native runtime

BugSplat captures crashes **out-of-process**: `BugSplat.dll` spawns `BugSplatMonitor.exe` from
the application's own directory, so these files **must sit next to your executable** at run time:

```
BugSplat.dll  BugSplatMonitor.exe  BugSplatRc.dll  BugSplatWer.dll
```

Declare them as `Content` with `CopyToOutputDirectory` so they flow to `dotnet build`,
`dotnet publish`, and MSIX packaging automatically (see the sample `.csproj` files).

### 3. Configure Windows Error Reporting (WER)

Fail-fast crashes — `__fastfail`, `/GS` stack-cookie failures, heap corruption — and crashes in
**packaged / WinUI 3** apps bypass the in-process filter and are captured only by BugSplat's WER
runtime-exception helper, `BugSplatWer.dll`. Register it by adding a `REG_DWORD` value **named
with the full path** to that `BugSplatWer.dll` (value data `0`) under:

```
HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\RuntimeExceptionHelperModules
```

This requires administrator rights; see step 3 of the
[BugSplat for Windows (C++)](cplusplus/README.md) guide. Check it at runtime with
`bugsplat.IsWerEnabled` and warn the user if it is `false` — the WinUI 3 sample does exactly this.

### 4. Upload symbols

Upload your app's PDBs (and `MyDotNetCrasherNative.pdb` for the native half of a mixed-mode
stack) with [symbol-upload](../../../development/working-with-symbol-files/) so crashes show file
names and line numbers.

### Mixed-mode C#/C++ crashes

When managed code P/Invokes native code that faults, BugSplat symbolicates one interleaved
managed+native stack (e.g. `MyDotNetCrasherNative!bscrash_fastfail → Program.<Main>$ → coreclr!…`).
The [`MyDotNetCrasher`](../../posting-a-test-crash/mydotnetcrasher-native-managed/README.md)
sample's `native-*` / `cpp-throw` modes exercise every boundary-crossing shape against the native
`MyDotNetCrasherNative.dll`.

### API reference

| Member | Purpose |
| --- | --- |
| `new BugSplat(database, application, version)` | Initialize native + managed reporting. |
| `HandleApplicationExceptions()` | Auto-post `AppDomain.UnhandledException` + unobserved task exceptions. |
| `Post(Exception)` | Post a caught managed exception (app keeps running). |
| `PostFeedback(title, description)` | Post non-crashing user feedback; returns a report id + URL. |
| `User` / `Email` / `Description` / `Notes` / `Key` / `SetAttribute` | Defaults stamped on every report. |
| `MiniDumpType` | Dump shape for native captures (defaults to a heap dump). |
| `IsWerEnabled` | Whether the WER helper is registered (see step 3). |
| `QuietMode` | Suppress the native crash dialog (still uploads). |

### Sample apps

{% content-ref url="../../posting-a-test-crash/mydotnetcrasher-native-managed/README.md" %}
[MyDotNetCrasher (.NET)](../../posting-a-test-crash/mydotnetcrasher-native-managed/README.md)
{% endcontent-ref %}

{% content-ref url="../../posting-a-test-crash/mydotnetwinui3crasher/README.md" %}
[MyDotNetWinUI3Crasher (WinUI 3)](../../posting-a-test-crash/mydotnetwinui3crasher/README.md)
{% endcontent-ref %}
