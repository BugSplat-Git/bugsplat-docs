---
description: >-
  Testing crashes, hangs, and user feedback in a WinUI 3 (.NET) app with the sample
  application 'MyDotNetWinUI3Crasher'
---

# MyDotNetWinUI3Crasher (WinUI 3)

`MyDotNetWinUI3Crasher` is a WinUI 3 (.NET 10) sample that reports crashes, non-fatal errors, and
user feedback with the
[BugSplat for .NET](../../integrations/desktop/bugsplat-for-dot-net.md)
(`BugSplatDotNet`) SDK. It is the GUI companion to the headless
[MyDotNetCrasher](../mydotnetcrasher-native-managed/README.md).

### Prerequisites 🚦

* [.NET 10 SDK](https://dotnet.microsoft.com/download) and the Windows App SDK workload
* A [BugSplat account](https://app.bugsplat.com/v2/sign-up) and a [database](../../create-a-new-database-in-bugsplat.md)
* Windows, x64

### 1. Get the sample and set your database

Clone [bugsplat-windows](https://github.com/BugSplat-Git/bugsplat-windows), open
`MyDotNetWinUI3Crasher.sln`, and set `App.Database` in `App.xaml.cs`. Upload the app's PDBs with
[symbol-upload](../../../development/working-with-symbol-files/) so crashes are symbolicated.

```
dotnet run -c Debug -p:Platform=x64
```

The app is **unpackaged** and **self-contained**, so it launches with no MSIX/certificate setup.

### 2. Configure Windows Error Reporting (required)

{% hint style="warning" %}
A WinUI 3 app's crashes are captured through BugSplat's WER runtime-exception helper
(`BugSplatWer.dll`). If it isn't registered, **crashes are not reported** — and the app shows a
warning at startup (`App` checks `BugSplat.IsWerEnabled`).
{% endhint %}

Add a `REG_DWORD` value **named with the full path** to the `BugSplatWer.dll` next to the built
exe (value data `0`) under
`HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\RuntimeExceptionHelperModules` (requires
administrator rights). See the
[integration guide](../../integrations/desktop/bugsplat-for-dot-net.md) for details.

### 3. Post a test crash

Each card triggers an event:

| Card | What it does |
| --- | --- |
| **Crash** | Unhandled managed exception — the C# stack is posted, then the process fail-fasts and the native handler captures a minidump. |
| **Non-Crash Error** | `BugSplat.Post(exception)` for a caught exception; the app keeps running. |
| **User Feedback** | `BugSplat.PostFeedback(...)`, returning a report id + info URL. |
| **Hang** | Freezes the UI thread; the native SDK's hang detection reports it. |
| **Mixed-Mode Crash** | P/Invokes `MyDotNetCrasherNative.dll` into a native access violation; BugSplat symbolicates one unified C#/C++ stack. |

### 4. View the crash ✅

Open the [Dashboard](https://app.bugsplat.com/v2/dashboard), select your database, and click a
report's **ID** for the symbolicated stack, the minidump, and system information.
