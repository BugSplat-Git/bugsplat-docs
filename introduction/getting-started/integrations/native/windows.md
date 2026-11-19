---
description: >-
  Add BugSplat Native 9.0 to a Windows application: ship the runtime, initialize,
  describe the report, capture hangs and feedback, register WER, upload symbols.
---

# Windows

{% hint style="success" %}
**Status: verified.** Windows is the reference platform for 9.0: access violations, stack overflows, C++ exceptions, `abort`, heap corruption, fail-fast through WER, heap dumps, hangs, structured reports and feedback have all been driven through the monitor and reporter against a live database.
{% endhint %}

### Requirements 📋

* Windows 10 or later, x64, x86 or ARM64.
* The Visual C++ 2015-2022 runtime on the end user's machine (`vcruntime140.dll`, `msvcp140.dll`, `vcruntime140_1.dll` on x64). Chain the matching `vc_redist` into your installer or ship the DLLs next to the BugSplat files.
* Build tools: CMake 3.24+ and Visual Studio 2022 or 2026 for the CMake package; any compiler for the C API through `BugSplat.dll`.

### What ships next to your executable 📦

| File | Purpose |
| --- | --- |
| `BugSplat.dll` | the library you link (import library `BugSplat.lib`) |
| `BugSplatMonitor.exe` | out-of-process capture, dump writing, crash signature, import into the store |
| `BugSplatReporter.exe` + `theme\` | the crash dialog, upload, support response; `theme.json` and `strings.<lang>.json` |
| `BugSplatWer.dll` | Windows Error Reporting helper (fail-fast, `/GS`, .NET runtime fail-fast) |

With CMake this is one line:

```cmake
find_package(bugsplat CONFIG REQUIRED)
target_link_libraries(my_app PRIVATE bugsplat::bugsplat)
bugsplat_install_runtime(TARGET my_app)   # copies the four files and theme\ next to my_app.exe
```

Without CMake, copy them from the release zip; `bugsplat_init()` looks next to your executable and next to `BugSplat.dll`, or wherever `bugsplat_options_set_monitor_path` / `set_reporter_path` point.

### Initialize 🏗️

```c
#include <bugsplat/bugsplat.h>

bugsplat_options* o = bugsplat_options_new("fred", "MyApp", "1.0.0");
bugsplat_options_set_upload_policy(o, BUGSPLAT_UPLOAD_DIALOG);          /* or QUIET, MANUAL */
bugsplat_options_set_dump_type(o, BUGSPLAT_DUMP_NORMAL);                /* HEAP or FULL for more memory */
bugsplat_options_set_hang_detection(o, 5000, BUGSPLAT_HANG_REPORT);     /* optional */
if (bugsplat_init(o) != BUGSPLAT_OK) {
    /* a packaging problem: BugSplatMonitor.exe or BugSplatReporter.exe is missing;
       details in bugsplat_log_file_path() */
}
```

The same in C++ (`bugsplat.hpp`):

```cpp
#include <bugsplat/bugsplat.hpp>

bugsplat::BugSplat g_bugsplat(bugsplat::Options("fred", "MyApp", "1.0.0")
                                  .UploadPolicy(bugsplat::UploadPolicy::Dialog)
                                  .HangDetection(5000));
```

And in .NET (`BugSplatDotNet` on NuGet; the package lays the runtime out per runtime identifier):

```csharp
using var bugsplat = new BugSplat("fred", "MyApp", "1.0.0");
bugsplat.HandleApplicationExceptions();   // managed exceptions become structured reports
```

Use the same application name and version you upload symbols under. `bugsplat_init` may be called once per process; it takes ownership of the options.

### Describe the report ✏️

Everything below can be called at any time after `bugsplat_init`, from any thread, and the value at the instant of the crash is what the report carries:

```c
bugsplat_set_user("ada@example.com");
bugsplat_set_email("ada@example.com");
bugsplat_set_key("level-3");                  /* selects the support response */
bugsplat_set_user_description("what the user was doing");
bugsplat_set_attribute("branch", "main");     /* up to 64; NULL value removes */
bugsplat_add_attachment("C:\\ProgramData\\MyApp\\app.log");   /* up to 24; copied at crash time */
printf("%s\n", bugsplat_get_environment());   /* "Windows 11 10.0.26200 x64"; override with bugsplat_set_environment */
```

Attributes appear on the crash page and in searches ([Using the Crash Attribute Feature](../../../../education/how-tos/using-the-crash-attribute-feature.md)); attachments are downloadable from the crash page.

### Reports that are not crashes 📝

```c
bugsplat_capture_report();                       /* dump the live process, keep running */

bugsplat_upload_result r = BUGSPLAT_UPLOAD_RESULT_INIT;
bugsplat_post_feedback("Title", "What the user typed", NULL, 0, &r);   /* r.crash_id, r.info_url */
bugsplat_upload_result_free(&r);
```

See [User Feedback](user-feedback.md) and [Structured Reports](structured-reports.md) (engines, scripting runtimes, managed exceptions, ASan output).

### Hang detection ⏱️

With a timeout set, the SDK pings your application's top-level windows every quarter of the timeout; an unanswered window is a hang, reported as a full dump with `reportKind=hang` and the "not responding" dialog. Console tools, services and game loops call `bugsplat_heartbeat()` instead. Details, policies and extra threads: [Hang Detection](hang-detection.md).

### Fail-fast crashes and WER 🪟

Some crashes never reach an in-process handler: `__fastfail`, `/GS` stack-buffer overruns, `RaiseFailFastException`, and the .NET runtime's own fail-fast after an unhandled managed exception. Windows Error Reporting handles those, and only calls a helper whose full path is allow-listed under `HKLM`. Your installer (running elevated) adds it once:

```
reg add "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\RuntimeExceptionHelperModules" /v "C:\Program Files\MyApp\BugSplatWer.dll" /t REG_DWORD /d 0
```

`bugsplat_has_capability(BUGSPLAT_CAP_WER)` answers 1 when the registration is in place. WinUI 3 applications need this for every crash, not just fail-fast.

### Heap and full memory dumps 🧠

```c
bugsplat_options_set_dump_type(o, BUGSPLAT_DUMP_HEAP);      /* private writable memory: CLR/GC/native heaps */
bugsplat_options_set_dump_type(o, BUGSPLAT_DUMP_FULL);      /* every readable region */
bugsplat_options_set_minidump_flags(o, MiniDumpWithFullMemory | MiniDumpWithHandleData);   /* raw MINIDUMP_TYPE */
```

The dump is written by DbgHelp while your process is suspended, so Visual Studio, WinDbg and BugSplat's .NET analysis see exactly what they see today. There is no client-side size gate: the server enforces your database's upload limit (100 MB by default; larger limits on Enterprise plans) when the reporter asks for the upload URL, and a refused report is logged as `rejected`. `BugSplatDotNet` defaults to `Heap` on Windows so managed frames resolve.

### Symbols 🔣

Windows reports are processed with your PDBs. Build release configurations with `/Zi` and `/DEBUG`, then upload after every build:

```batch
symbol-upload-windows.exe -b your-database -a MyApp -v 1.0.0 -i your-client-id -s your-client-secret -d "$(OutDir)" -f "**/*.{pdb,exe,dll}"
```

or, in CMake, `bugsplat_upload_symbols(TARGET my_app DATABASE your-database APPLICATION MyApp VERSION 1.0.0)` with `BUGSPLAT_CLIENT_ID`/`BUGSPLAT_CLIENT_SECRET` in the environment. See [Upload Symbols with symbol-upload](../../../development/working-with-symbol-files/upload-symbols-with-symbol-upload.md).

### Branding the dialog 🎨

`theme\theme.json` next to `BugSplatReporter.exe` controls colours, logo, layout and features such as "Always send"; `theme\strings.<lang>.json` holds every string, per language. Preview without crashing anything:

```
BugSplatReporter.exe --preview --theme "C:\work\my-theme"
```

Reference: [THEME.md](https://github.com/BugSplat-Git/bugsplat-native/blob/main/reporter/docs/THEME.md) and [STRINGS.md](https://github.com/BugSplat-Git/bugsplat-native/blob/main/reporter/docs/STRINGS.md).

### Where things are 🔍

* Reports and the log: `%LOCALAPPDATA%\BugSplat\<app>-<version>\`. `BugSplat.log` there is the first place to look; each report folder has its own log too.
* Pending reports (the `MANUAL` policy, or a machine that was offline) are retried on the next launch, up to three attempts for server errors.
* The user's name and email are remembered in `preferences.json` in that folder, not in the registry.

### Troubleshooting 🛠️

| Symptom | Cause |
| --- | --- |
| `bugsplat_init` returns `BUGSPLAT_ERR_MONITOR_NOT_FOUND` / `_REPORTER_NOT_FOUND` | the helper executables are not next to your exe or `BugSplat.dll`; run `bugsplat_install_runtime` or fix the installer |
| Crash dialog appears, then "MSVCP140.dll was not found" | the Visual C++ runtime is missing on the machine |
| Fail-fast crashes (`0xC0000409`) are not reported | `BugSplatWer.dll` is not allow-listed under `HKLM`; `bugsplat_has_capability(BUGSPLAT_CAP_WER)` is 0 |
| No dialog when crashing from a CI or agent shell | the shell runs children in a Job Object that kills them on an unhandled exception; launch the crasher outside it (for example through WMI `Win32_Process.Create`) |
| Report uploaded but shows no symbols | upload PDBs under the same application name and version; see [Why are Crashes Missing Symbols](../../../../education/faq/why-are-crashes-missing-symbols-function-names-and-or-line-numbers.md) |

Upgrading from BugSplat for Windows 8.x? See [Migrating to 9.0](migration.md).
