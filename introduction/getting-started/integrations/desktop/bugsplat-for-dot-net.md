---
description: >-
  Add crash, hang, and user-feedback reporting to a modern .NET (net8.0 / net10.0)
  application with the BugSplatDotNet SDK
---

# BugSplat for .NET

{% hint style="info" %}
This is the SDK for **modern .NET** (.NET 8 / .NET 10). For the classic .NET Framework SDK see
[.NET Framework](windows-dot-net-framework.md); for the cross-platform managed transport this SDK
builds on, see [.NET Standard](dot-net-standard.md).
{% endhint %}

### Overview 👀

`BugSplatDotNet` combines two complementary layers behind one `BugSplat` class:

* **Managed (`BugSplatDotNetStandard`)** — posts unhandled managed `Exception`s (with their C#
  stack traces). This layer is **cross-platform**.
* **Native (`BugSplat.dll`)** — on **Windows**, installs the native unhandled-exception filter
  and the WER handler and spawns `BugSplatMonitor.exe` on a genuine hardware/native fault (access
  violation, fail-fast, stack overflow, corrupted heap, or a crash inside P/Invoke) — crashes the
  CLR never surfaces as managed exceptions. It also captures mixed **C#/C++** crashes as a single,
  unified stack that crosses the managed/native boundary.

### Platform support

| Platform | Managed exception reporting | Native crash capture |
| --- | --- | --- |
| **Windows (x64)** | ✅ | ✅ full — hard faults, hangs, WER fail-fasts, mixed-mode C#/C++ |
| **Linux / macOS** | ✅ via [`BugSplatDotNetStandard`](dot-net-standard.md) | 🚧 on the [roadmap](#roadmap) |

Modern .NET runs everywhere, and native crash capture is currently implemented on **Windows**. On
other platforms you get managed exception reporting today; native capture is tracked on the
[roadmap](#roadmap) below. The Windows-specific steps in this guide (shipping the native runtime,
configuring WER) apply only where native capture is available.

### Getting Started 🚦

The SDK and its Windows sample apps live in the
[bugsplat-windows](https://github.com/BugSplat-Git/bugsplat-windows) repository under
`BugSplatDotNet/` and `Samples/`. Reference `BugSplatDotNet.csproj` from your app (or copy the
files listed in `BugSplatDotNetFiles.txt`). The Windows native layer is **x64** — it links the
native `BugSplat.dll`, which ships x64.

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

### 2. Ship the native runtime (Windows)

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

### Roadmap

Native crash capture is implemented on Windows today. Cross-platform support is tracked in
[bugsplat-windows](https://github.com/BugSplat-Git/bugsplat-windows), where the SDK lives:

* Guard the native P/Invoke by OS + managed-only fallback off Windows — [#173](https://github.com/BugSplat-Git/bugsplat-windows/issues/173)
* Native crash capture on Linux — [#174](https://github.com/BugSplat-Git/bugsplat-windows/issues/174)
* Native crash capture on macOS — [#175](https://github.com/BugSplat-Git/bugsplat-windows/issues/175)
* Ship as a NuGet package with per-RID native assets — [#176](https://github.com/BugSplat-Git/bugsplat-windows/issues/176)
* Cross-platform sample + CI matrix — [#177](https://github.com/BugSplat-Git/bugsplat-windows/issues/177)

Until those land, use `BugSplat.Post(exception)` for managed exception reporting on Linux/macOS.

### Sample apps

{% content-ref url="../../posting-a-test-crash/mydotnetcrasher-native-managed/README.md" %}
[MyDotNetCrasher (.NET)](../../posting-a-test-crash/mydotnetcrasher-native-managed/README.md)
{% endcontent-ref %}

{% content-ref url="../../posting-a-test-crash/mydotnetwinui3crasher/README.md" %}
[MyDotNetWinUI3Crasher (WinUI 3)](../../posting-a-test-crash/mydotnetwinui3crasher/README.md)
{% endcontent-ref %}
