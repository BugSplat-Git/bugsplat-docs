# Unity on Windows

BugSplat for Unity captures native crashes on Windows with [BugSplat for Windows](../../desktop/cplusplus/README.md). Native Windows crash reporting works with both the **Mono** and **IL2CPP** scripting backends, on x86 (32-bit), x64, and Windows-on-ARM (ARM64) players. It is on by default (`UseNativeCrashReportingForWindows`).

When native crash reporting is enabled:

* Native crashes are captured at crash time and uploaded by `BugSplatMonitor.exe` in the background — straight away, or once the user clicks **Send** in the crash dialog. Reports that can't be uploaded (for example, when the user is offline) are uploaded automatically on the next launch.
* `Player.log` is attached to native crash reports when `CapturePlayerLog` is enabled on your options asset. Setting the `CapturePlayerLog` property at runtime adds or removes the attachment.
* The BugSplat crash dialog is shown by default. It runs as its own process, `BugSplatReporter.exe`: **Send** uploads the report with the name, email, and description the user entered, and **Don't send** discards it. Set `WindowsShowCrashDialog` to `false` to send reports silently instead.
* Fail-fast crashes — stack buffer overruns and heap corruption — bypass BugSplat's crash handler entirely and need one extra install-time step. See [Windows Error Reporting](windows.md#windows-error-reporting).

### Shipping Your Game

At build time, BugSplat copies `BugSplatMonitor.exe`, `BugSplatReporter.exe`, `BugSplatWer.dll`, and the `BugSplatTheme` folder next to your game's executable. These are required for crash reporting and must be shipped alongside your game's executable in your installer. Without `BugSplatReporter.exe`, reports still upload, but silently, with no dialog; without `BugSplatTheme`, the dialog falls back to its built-in look.

{% hint style="warning" %}
The native library depends on the Microsoft Visual C++ Redistributable (`vcruntime140.dll`, `msvcp140.dll`), which Unity Windows players already require. If the redistributable is missing on an end user's machine, native crash reporting fails to initialize with an error in the log, and .NET exception reporting continues to work.
{% endhint %}

### Crash Dialog

The dialog reads its colors, fonts, layout, logo, and wording at run time from the `BugSplatTheme` folder next to `BugSplatReporter.exe`. Nothing is compiled, so rebranding it means editing files, not rebuilding anything. [Crash Dialog Branding](../../../../../education/how-tos/customize-the-crash-dialog.md) documents the theme format, how to preview a theme with `BugSplatReporter.exe --preview` without crashing anything, and how to check one with `--check-theme`. Both commands work against the copy of the reporter in your build folder.

The Unity package doesn't have a setting for a custom theme yet: every Windows build copies the default theme from the package, replacing whatever is in the build folder's `BugSplatTheme`. To ship your own, put your theme in that folder in your installer, after the build.

### Windows Symbols

Symbol upload is on by default for Windows (`UploadDebugSymbolsForWindows`) and runs as a post-build step. It needs [symbol upload credentials](symbol-upload.md), generated on the BugSplat [Integrations](https://app.bugsplat.com/v2/settings/database/integrations) page.

BugSplat uploads the `.pdb`, `.dll`, and `.exe` files in your build folder, including the `.dll` and `.pdb` files of any native C++ plugins in `Assets/Plugins/x86` and `Assets/Plugins/x86_64`. For IL2CPP builds, BugSplat also copies `LineNumberMappings.json` into the build folder and uploads it, so IL2CPP-generated C++ frames symbolicate back to C# method names, file names, and line numbers.

Enable **Copy PDB Files** under Build Settings > Windows, or the build contains no `.pdb` files and crash reports will not symbolicate. A Windows editor checks this setting and skips the upload when it's off; on any other host the setting can't be read, so the upload runs and a warning is logged.

### Windows Hang Detection

Set `WindowsHangDetectionTimeoutMs` to a non-zero value to report hangs when your game's main thread stops responding for longer than the configured timeout. When a hang is detected, BugSplat captures a hang report, uploads it, and **terminates the hung process**.

Hang detection is **disabled by default** (`0`) because long frames — such as loading screens or synchronous asset operations — can be falsely reported as hangs, and a false positive terminates your game. If you enable hang detection, choose a timeout comfortably longer than your game's longest expected frame.

### Windows Error Reporting

A few crash types terminate a process without giving any in-process code a chance to run. The most common are stack buffer overruns (`0xC0000409`, which is also what `__fastfail` produces) and heap corruption (`0xC0000374`). BugSplat's crash handler never sees these, so Windows Error Reporting has to hand them over instead — that is what `BugSplatWer.dll` is for.

Two things must be true for it to work:

1. **`BugSplatWer.dll` sits next to your game's executable.** The post-build step already does this when `UseNativeCrashReportingForWindows` is enabled.
2. **A machine-wide registry value names that DLL's full path.** Under `HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\RuntimeExceptionHelperModules`, add a `REG_DWORD` whose **name** is the absolute path to the installed `BugSplatWer.dll` (the data is ignored). This lives in `HKLM`, so writing it requires administrator rights.

**Your installer is responsible for step 2**, and for removing the value on uninstall:

```bat
reg add "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\RuntimeExceptionHelperModules" /v "C:\Program Files\MyGame\BugSplatWer.dll" /t REG_DWORD /d 0 /f
reg delete "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\RuntimeExceptionHelperModules" /v "C:\Program Files\MyGame\BugSplatWer.dll" /f
```

The value name must match the installed path exactly, using backslashes. Moving or reinstalling the game to a different folder silently disarms it.

For local builds, use **BugSplat > Windows > Register WER Handler** in the editor. It asks for your built player, writes the value elevated, and reads it back to confirm. **Check WER Handler Registration** reports the current state, and **Unregister WER Handler** removes it.

At runtime, `bugsplat.WindowsWerEnabled` tells you whether the handler registered. When it hasn't, BugSplat logs what will be missed and how to fix it — as a warning in development builds and an informational message otherwise, since end users can't act on it. All other crashes are reported normally either way.

If registration appears to succeed but `WindowsWerEnabled` stays `false`, check your endpoint-protection software: `RuntimeExceptionHelperModules` is a known persistence location and some products monitor or block writes to it.

### Troubleshooting

When a report doesn't arrive, look in two places: `%TEMP%\BugSplat\<Application>-<Version>\<GUID>\` holds BugSplat's own logs, including `BugSplatWer.log`, and `%LOCALAPPDATA%\CrashDumps` collects dumps Windows wrote because nothing claimed the crash.

{% hint style="info" %}
BugSplat installs its handler with `SetUnhandledExceptionFilter` and then prevents that filter from being replaced, so other middleware in your project cannot install a top-level exception filter after BugSplat initializes.
{% endhint %}
