---
description: >-
  How BugSplat for Windows captures a crash, shows the crash dialog, and uploads
  the report
---

# How the Windows Crash Reporter Works

BugSplat for Windows is a handful of files that ship next to your executable. Knowing which one does what makes it much easier to work out what went wrong when a report doesn't arrive, or when the crash dialog doesn't appear. This page describes BugSplat for Windows 9.0.0 and later.

{% hint style="warning" %}
**Upgrading from 8.x?** `BugSplatReporter.exe` is a **new file** in 9.0.0, and `BugSplatRc.dll` is **gone**. If you update the SDK but not your installer, crash reports still upload, but the crash dialog silently disappears and your users are never asked for a description. See [Upgrading to 9.0.0](bugsplat-for-windows-upgrade-guide.md#upgrading-to-9.0.0).
{% endhint %}

### The files you ship 📦

| File | What it does |
| --- | --- |
| `BugSplat.dll` | The crash reporting engine that loads into your application. Installs the exception filter, allocates guard memory, holds the user, notes and attributes you set, and signals the monitor when something goes wrong. Only needed if you link the import library rather than a static `BugSplat.lib`, or use BugSplat for .NET. |
| `BugSplatMonitor.exe` | Runs in a separate process so it survives the crash. Reads the shared memory your application wrote, calls `MiniDumpWriteDump` against the crashing process, detects hangs, assembles the crash folder, and **uploads the report**. |
| `BugSplatReporter.exe` | The **crash dialog** your end user sees, and nothing else. It never touches the network; it hands the user's answers back to the monitor. |
| `BugSplatWer.dll` | The Windows Error Reporting runtime exception module. Catches the failures no in-process filter can see — fast-fail errors, stack buffer overruns, and some heap corruption. Registered in the registry by your installer. |
| `theme\` (optional) | `theme.json` and `strings.en-US.json`, which `BugSplatReporter.exe` reads to decide how the crash dialog looks and what it says, plus any logo or translations you add. |

`BugSplatRc.dll`, the resource-only DLL that held the dialog templates and artwork before 9.0.0, no longer exists. Don't ship it.

{% hint style="info" %}
The `theme` folder in the SDK's `bin` folder is an exact copy of the defaults built into `BugSplatReporter.exe`, so the dialog looks the same with or without it. Ship it when you rebrand or reword the dialog. See [Crash Dialog Branding](../../../../../education/how-tos/customize-the-crash-dialog.md).
{% endhint %}

### The dialog is a separate process 🧩

Before 9.0.0, the crash dialog was part of `BugSplatMonitor.exe`. It now lives in its own executable. The monitor still does everything else — capture, upload and retries — and the two communicate through the crash folder and the reporter's exit code rather than through an IPC protocol.

`BugSplatMonitor.exe` writes everything it knows about the crash into a crash folder, then runs:

```
BugSplatReporter.exe --report "C:\Users\<user>\AppData\Local\Temp\BugSplat\<app>-<version>\<guid>"
```

That folder path is the reporter's input. It reads the application name and version and the user's name, email and description from `BugSplatCrashData.json` to fill in the dialog. The monitor also passes `--link-domains` with the domains your application allowed with `SetCrashDialogLinkDomains`, so that only your application's code, never a file on disk, decides which [links in the dialog](../../../../../education/how-tos/customize-the-crash-dialog.md#links-in-the-dialog) work.

The reporter answers with its exit code:

| Exit code | Meaning | What the monitor does |
| --- | --- | --- |
| `0` | **Send report.** The reporter wrote the user's name, email and description to `dialog-result.json` first. | Saves the answers into the crash data, then uploads the report. |
| `2` | **Don't send.** | Deletes the crash folder. Nothing is uploaded. |
| Anything else, or the reporter couldn't be started | The dialog failed. | Uploads the report as it stands, so a problem with the dialog never costs you the crash. |

The split has three practical consequences:

* **Theming needs no compiler.** The reporter reads its appearance from JSON at runtime, so rebranding the dialog is editing a file rather than rebuilding a resource DLL in Visual Studio.
* **Your application doesn't wait for the upload.** As soon as the user answers the dialog, the monitor releases your application and uploads in the background. There's no progress window.
* **A missing or broken reporter degrades rather than fails.** The monitor does the uploading either way, so the report still arrives without the dialog.

### A crash, end to end 🔄

```
  YOUR APPLICATION                          WINDOWS ERROR REPORTING
  BugSplat.dll / BugSplat.lib               BugSplatWer.dll
  exception filter + shared memory          fast-fail, stack overrun
            │                                          │
            │ 1. launch at init                        │
            │ 2. signal on crash / hang                │
            └─────────────────┬────────────────────────┘
                              ▼
                  ┌───────────────────────┐
                  │  BugSplatMonitor.exe  │   3. MiniDumpWriteDump
                  │                       │   4. write the crash folder:
                  │                       │      minidump, BugSplat.log,
                  │                       │      attachments,
                  │                       │      BugSplatCrashData.json
                  └───────────┬───────────┘
                              │
              5. spawn and wait for exit
                 --report <crash folder>
                              │
                              ▼
                  ┌───────────────────────┐
                  │ BugSplatReporter.exe  │   reads theme\theme.json
                  │                       │   and theme\strings.<tag>.json
                  │   the crash dialog    │   6. show the crash dialog
                  └───────────┬───────────┘
                              │
              7. exit code, and on Send
                 dialog-result.json { user, email, description }
                              │
                              ▼
                  ┌───────────────────────┐
                  │  BugSplatMonitor.exe  │   8. save the answers,
                  │                       │      release your application
                  │                       │   9. zip and upload to BugSplat
                  └───────────────────────┘  10. delete the crash folder
```

1. Your application constructs `BugSplat` (or calls `BugSplat_Init`). The SDK launches `BugSplatMonitor.exe` and hands it a shared memory block and a pair of events.
2. Your application crashes, or hangs past `SetHangDetectionTimeout`. The exception filter fills in the shared memory and signals the monitor. If the failure is one no in-process filter can see, Windows Error Reporting loads `BugSplatWer.dll`, which writes the minidump into the crash folder itself and then asks the monitor to send it.
3. Otherwise, the monitor — a live process, unaffected by the corruption in yours — calls `MiniDumpWriteDump` against the crashing process.
4. The monitor writes a crash folder under `%TEMP%\BugSplat\<appName>-<appVersion>\<guid>\`, containing the minidump, `BugSplat.log`, any attachments you added, and `BugSplatCrashData.json`.
5. The monitor spawns `BugSplatReporter.exe --report <folder>` and waits for it. Your application waits too.
6. The reporter loads `theme\theme.json` and the string file matching the end user's Windows UI language, then shows the crash dialog, prefilled with the name, email and description your application set.
7. On **Send report**, the reporter writes what the user entered to `dialog-result.json` and exits with `0`. On **Don't send**, or if the user closes the window, it exits with `2`. Pressing Escape does nothing, so a reflexive key press doesn't throw the report away.
8. On Send, the monitor saves the answers into `BugSplatCrashData.json` and releases your application, which can now exit. On Don't send, it deletes the crash folder and stops.
9. The monitor zips the folder and uploads it to BugSplat, then opens your [support response](../../../../production/setting-up-custom-support-responses.md) in the browser, if you have one for this crash.
10. On success the monitor deletes the crash folder. If the upload fails with an error worth retrying, it increments a retry count and leaves the folder in place, so a later launch of your application can try again, up to three times.

Because the answers are saved before the upload starts, an upload cut short — the machine shuts down, or a launcher ends your application's whole process tree — isn't lost: the next launch of your application uploads the folder with the user's description in it.

{% hint style="info" %}
`BugSplatCrashData.json` and `dialog-result.json` are deliberately **excluded** from the uploaded zip. They're the monitor's working state, not part of the report; the user's name, email and description are sent in the fields they belong in.
{% endhint %}

### Quiet mode and unattended machines 🤫

`SetQuietMode(true)` (`BugSplat_SetQuietMode(1)` in C, or `QuietMode = true` in .NET) tells the monitor not to start `BugSplatReporter.exe` at all. The monitor uploads the report with no window: no dialog and no support response. In quiet mode your application waits until the upload has finished.

For machines that have a screen but nobody in front of them — kiosks, build agents, test rigs — `features.autoCloseSeconds` in `theme.json` can be a better fit than quiet mode: the dialog appears, and sends itself if nobody touches it. It never discards the report.

### When `BugSplatReporter.exe` is missing 🚨

If `BugSplatReporter.exe` isn't next to `BugSplatMonitor.exe`, or can't be started, the monitor uploads the report without showing the dialog.

**The report still arrives.** What is lost is the dialog — so the user is never prompted for a description and has no opportunity to decline. From your side the only visible symptom is that crashes suddenly stop carrying user descriptions.

This is logged. `BugSplat.log` in the crash folder will contain:

```
BugSplatReporter.exe not found next to BugSplatMonitor.exe - uploading without the crash dialog. Add BugSplatReporter.exe to your installer.
```

If the reporter is there but can't be started, the log says `Failed to start BugSplatReporter.exe`, with the Windows error code, instead. Either line is the thing to search for if the dialog stops appearing after an SDK update.

{% hint style="danger" %}
Updating `BugSplat.dll` and `BugSplatMonitor.exe` without adding `BugSplatReporter.exe` is the failure mode to watch for. It doesn't fail, it doesn't warn the end user, and reports keep arriving; the dialog just quietly stops appearing. See [Upgrading to 9.0.0](bugsplat-for-windows-upgrade-guide.md#upgrading-to-9.0.0).
{% endhint %}

### Customizing the dialog 🎨

Colors, fonts, measurements, the logo and which fields appear all come from `theme\theme.json`. Every word the dialog shows comes from `theme\strings.en-US.json`, or from a translation you add. Both are read at run time, and `BugSplatReporter.exe --preview` shows the result without crashing anything or uploading a report.

See [Crash Dialog Branding](../../../../../education/how-tos/customize-the-crash-dialog.md).

### Xbox and GDK 🎮

There is no `BugSplatReporter.exe` or `theme` folder on Xbox, because there is no crash dialog. `BugSplatMonitor.exe` posts the report itself, and quiet mode is always on. Crash folders live under `XPersistentLocalStorageGetPath` rather than `%TEMP%`. See [Xbox](../../game-development/xbox.md).
