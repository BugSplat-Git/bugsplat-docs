---
description: >-
  How BugSplat for Windows captures a crash, shows the crash dialog, and uploads
  the report
---

# How the Windows Crash Reporter Works

BugSplat for Windows is four files that ship next to your executable. Knowing which one does what makes it much easier to work out what went wrong when a report doesn't arrive, or when the crash dialog doesn't appear.

{% hint style="warning" %}
**Upgrading?** `BugSplatReporter.exe` is a **new file** that did not exist in earlier releases, and `BugSplatRc.dll` is **gone**. If you update `BugSplat.dll` but not your installer, crash reports still upload — but the crash dialog silently disappears and your users are never asked for a description. See [Upgrading an existing installer](#upgrading-an-existing-installer) below.
{% endhint %}

### The files you ship 📦

| File | What it does |
| --- | --- |
| `BugSplat.dll` | The crash reporting engine that loads into your application. Installs the exception filter, allocates guard memory, holds the user, notes and attributes you set, and signals the monitor when something goes wrong. Only needed if you link the import library rather than the static `BugSplat.lib`. |
| `BugSplatMonitor.exe` | Crash **capture**, in a separate process so it survives the crash. Reads the shared memory your application wrote, calls `MiniDumpWriteDump` against the crashing process, detects hangs, and assembles the crash folder. |
| `BugSplatReporter.exe` | Crash **reporting** — the dialog your end user sees, the progress window, and the upload that follows. |
| `BugSplatWer.dll` | The Windows Error Reporting runtime exception module. Catches the failures no in-process filter can see — fast-fail errors, stack buffer overruns, and some heap corruption. Registered in the registry by your installer. |

Four files before this change, four files after. `BugSplatRc.dll` — the resource-only DLL that used to hold the dialog templates and artwork — has been removed, and `BugSplatReporter.exe` takes its place in the list.

{% hint style="info" %}
Alongside those four, the SDK's `bin` folder contains a `theme` folder holding `theme.json` and `strings.en-US.json`. It is optional — the reporter falls back to a built-in copy of both if the folder is absent — but you need it if you want to rebrand or reword the dialog. See [Crash Dialog Branding](../../../../../education/how-tos/customize-the-crash-dialog.md).
{% endhint %}

### Capture and reporting are separate processes 🧩

The crash dialog used to be compiled into `BugSplatMonitor.exe`. It now lives in its own executable, and the two communicate through a folder on disk rather than through an IPC protocol.

`BugSplatMonitor.exe` writes everything it knows about the crash into a crash folder, then runs:

```
BugSplatReporter.exe --report "C:\Users\<user>\AppData\Local\Temp\BugSplat\<app>-<version>\<guid>"
```

That folder path is the reporter's **entire** input. `BugSplatCrashData.json` inside it already carries the database, application name and version, the user's name, email and description, notes, attributes, the key, quiet mode and the crash type — so there is nothing else to pass.

The split has three practical consequences:

* **Theming needs no compiler.** The reporter reads its appearance from JSON at runtime, so rebranding the dialog is editing a file rather than rebuilding a resource DLL in Visual Studio.
* **The dialog and the upload share a process.** The progress bar reflects the actual upload, with no cross-process progress channel to go wrong.
* **A missing reporter degrades rather than fails.** `BugSplatMonitor.exe` links the same uploader the reporter uses, so it can post the report itself if `BugSplatReporter.exe` is not there.

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
                  │  ─────────────────    │   4. write the crash folder:
                  │  crash CAPTURE        │      minidump, BugSplat.log,
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
                  │ ─────────────────     │   and theme\strings.<tag>.json
                  │ crash REPORTING       │   6. show the crash dialog
                  │                       │   7. zip and POST to bugsplat.com
                  └───────────┬───────────┘
                              │
              8. result.json back in the crash folder
                 { hr, httpStatus, crashId, infoUrl, cancelled }
                 9. crash folder deleted on success
                              │
                              ▼
                  10. monitor releases your application
```

1. Your application constructs `BugSplat` (or calls `BugSplat_Init`). The SDK launches `BugSplatMonitor.exe` and hands it a shared memory block and a pair of events.
2. Your application crashes, or hangs past `SetHangDetectionTimeout`. The exception filter fills in the shared memory and signals the monitor. If the failure is one no in-process filter can see, Windows Error Reporting calls `BugSplatWer.dll`, which signals the monitor the same way.
3. The monitor — a live process, unaffected by the corruption in yours — calls `MiniDumpWriteDump` against the crashing process.
4. The monitor writes a crash folder under `%TEMP%\BugSplat\<appName>-<appVersion>\<guid>\`, containing the minidump, `BugSplat.log`, any attachments you added, and `BugSplatCrashData.json`.
5. The monitor spawns `BugSplatReporter.exe --report <folder>` and waits for it.
6. The reporter loads `theme\theme.json` and the string file matching the end user's Windows UI language, then shows the crash dialog. The user's name, email and description are written back into `BugSplatCrashData.json`.
7. On **Send Error Report**, the reporter zips the folder, posts it to BugSplat, and shows a progress window. On **Don't Send**, the report is discarded.
8. The reporter writes `result.json` into the crash folder and exits. The monitor reads that file rather than the exit code.
9. On success the reporter deletes the crash folder. On failure it increments a retry count and leaves the folder in place, so the next launch of your application can try again.
10. The monitor releases your application and exits.

{% hint style="info" %}
`BugSplatCrashData.json` is deliberately **excluded** from the uploaded zip. It holds the user's name, email and description as your application set them, before the dialog gave the user a chance to change or clear them.
{% endhint %}

### Quiet mode and unattended machines 🤫

`SetQuietMode(true)` (or `BugSplat_SetQuietMode`) sets `quietMode` in the crash data, and the monitor adds `--quiet` to the reporter's command line. The reporter uploads with no window at all.

For machines that have a screen but nobody in front of them — kiosks, build agents, test rigs — `features.autoCloseSeconds` in `theme.json` is usually a better fit than quiet mode: the dialog appears, and sends itself if nobody touches it. It never discards the report.

### When `BugSplatReporter.exe` is missing 🚨

`BugSplatMonitor.exe` links the same uploader that the reporter uses. If `BugSplatReporter.exe` is not next to it, or cannot be started, the monitor uploads the report in-process instead.

**The report still arrives.** What is lost is the dialog — so the user is never prompted for a description, never sees a support response, and has no opportunity to decline. From your side the only visible symptom is that crashes suddenly stop carrying user descriptions.

This is logged. `BugSplat.log` in the crash folder will contain:

```
BugSplatReporter.exe not found next to BugSplatMonitor.exe - uploading in-process
without the crash dialog. Add BugSplatReporter.exe to your installer.
```

That line is the thing to search for if the dialog stops appearing after an SDK update.

### Upgrading an existing installer

If you are coming from a release that shipped `BugSplatRc.dll`, make these two changes together:

1. **Add `BugSplatReporter.exe`** to your installer, in the same folder as your executable and `BugSplatMonitor.exe`.
2. **Remove `BugSplatRc.dll`.** It is no longer built and nothing loads it.

Add the `theme` folder as well if you intend to customize the dialog. Nothing breaks without it — the reporter has a built-in copy of both files.

No code changes are required. The API is unchanged, and neither `BugSplatMonitor.exe` nor `BugSplatReporter.exe` is named anywhere in your source.

{% hint style="danger" %}
Updating `BugSplat.dll` and `BugSplatMonitor.exe` without adding `BugSplatReporter.exe` is the failure mode to watch for. It does not error, it does not warn the end user, and reports keep arriving — the dialog just quietly stops appearing.
{% endhint %}

### Customizing the dialog 🎨

Colours, fonts, measurements, the logo and which fields appear all come from `theme\theme.json`. Every word the dialog shows comes from `theme\strings.en-US.json`. Both are read at runtime.

See [Crash Dialog Branding](../../../../../education/how-tos/customize-the-crash-dialog.md).

### Xbox and GDK 🎮

There is no `BugSplatReporter.exe` on Xbox, because there is no crash dialog. `BugSplatMonitor.exe` links the uploader and posts the report itself. Crash folders live under `XPersistentLocalStorageGetPath` rather than `%TEMP%`. See [Xbox](../../game-development/xbox.md).
