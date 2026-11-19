---
description: >-
  Moving to BugSplat Native 9.0 from BugSplat for Windows 8.x, bugsplat-apple
  2.x and bugsplat-android 1.x: same concepts, new names, no shims.
---

# Migrating to 9.0

BugSplat Native keeps the concepts you already use (database/application/version, user, email, key, description, notes, attributes, attachments, quiet mode, the dialog, the support response) and changes the names and the packaging. There is deliberately **no compatibility layer** in the client: the old headers, exports and store formats are gone, and the server keeps accepting reports from the old SDKs for as long as you need to switch. Upgrade an application in one step, on your own schedule.

### From BugSplat for Windows 8.x (C++ and C)

**Files.** `BugSplat.h`/`BugSplatC.h` (UTF-16) become `bugsplat/bugsplat.h` (UTF-8 C API) and `bugsplat/bugsplat.hpp` (C++ wrapper). `BugSplatRc.dll` is gone. What ships next to your executable keeps its names: `BugSplat.dll`, `BugSplatMonitor.exe`, `BugSplatWer.dll`, `BugSplatReporter.exe` (plus its `theme\`). The static `BugSplat.lib` build is gone; link the import library and ship `BugSplat.dll`, which removes the `/MT` vs `/MD` coupling.

**Methods.** The C++ wrapper keeps the 8.x names where they made sense:

| 8.x | 9.0 C++ (`bugsplat::BugSplat`) | 9.0 C |
| --- | --- | --- |
| `BugSplat g(db, app, ver)` | `bugsplat::BugSplat g(db, app, ver)` or `g(bugsplat::Options(db, app, ver)...)` | `bugsplat_options_new` + `bugsplat_init` |
| `SetUser` / `SetEmail` / `SetUserDescription` / `SetNotes` | same | `bugsplat_set_user` / `_email` / `_user_description` / `_notes` |
| `SetKey` (app key) | `SetKey` | `bugsplat_set_key` |
| `SetAttribute(name, value)` | `SetAttribute` / `RemoveAttribute` | `bugsplat_set_attribute(name, value \| NULL)` |
| `AddAttachment(path)` | `AddAttachment` / `RemoveAttachment` | `bugsplat_add_attachment` / `_remove_attachment` |
| `SetQuietMode(true)` | `SetQuietMode` or `Options::UploadPolicy(Quiet)` | `bugsplat_set_quiet_mode` |
| `SetMiniDumpType(MINIDUMP_TYPE)` | `Options::DumpType(Heap \| Full)` or `MinidumpFlags(...)` | `bugsplat_options_set_dump_type` / `_minidump_flags` |
| `SetHangDetectionTimeout(seconds)` | `Options::HangDetection(ms, policy)` | `bugsplat_options_set_hang_detection` |
| `CreateXmlReport(xml)` | `bugsplat::Report` builder + `Post()` | `bugsplat_report_*` + `bugsplat_report_post` |
| `CreateAsanReport(text)` | `PostAsanReport` | `bugsplat_post_asan_report` |
| `PostFeedback` / `PostFeedbackWithResult` | `PostFeedback` returning `UploadResult` | `bugsplat_post_feedback` |
| `IsWerEnabled()` | `HasCapability(BUGSPLAT_CAP_WER)` | `bugsplat_has_capability` |
| `AllocGuardMemory` | not needed (capture is out of process; nothing is allocated at crash time) | |
| global exception filter callbacks | `Options::OnCrash(fn)` (async-signal-safe, no BugSplat calls) or `CrashCompletion(ContinueSearch)` to run your own filter after the dump | `bugsplat_options_set_on_crash` / `_crash_completion` |

**Behaviour changes.**

* Attributes and attachments can now be changed **at any time**, not only before a crash is possible; the value at the crash instant is what is sent.
* Heap and full dumps no longer consult the server before writing (`/api/fullDumpFlag` is gone); the server enforces the upload size limit when the report is sent.
* The user's remembered name and email moved from `HKCU\Software\BugSplat\UserCredentials` to `preferences.json` in the store; the store moved to `%LOCALAPPDATA%\BugSplat\<app>-<version>\`.
* The dialog is themed with `theme.json` and translated with `strings.<lang>.json`; resource-DLL customization is gone.
* `Environment` is new: detected automatically, shown on the crash page, overridable.
* Symbols: still PDBs, uploaded exactly as before.

### From bugsplat-apple 2.x (macOS, iOS, tvOS)

* PLCrashReporter is replaced by Crashpad: out of process on macOS (`BugSplatMonitor` and `BugSplatReporter.app` inside your bundle), in process on iOS/tvOS with the report sent on the next launch, as before.
* `BugSplat.shared().start()` with `Info.plist` keys becomes `BugSplat.start(database:application:version:)` with an options closure; `BugSplatDelegate` callbacks for attachments become `addAttachment` at any time; `autoSubmitCrashReport` becomes the upload policy; `askUserDetails`, `persistUserDetails` and the banner image become theme and preference settings; `postFeedback(...)` keeps its shape.
* Hang detection is the shared watchdog: non-fatal reports with a full dump while the app runs, in addition to the fatal case.
* Symbols: still dSYMs, uploaded with `symbol-upload -m` as Breakpad `.sym` (the same command as before).
* Reports post as crash type 5 with the `environment` field naming the platform; the `.crashlog` types are no longer produced.

### From bugsplat-android 1.x

* The Crashpad handler you built and the `crashpad.php` URL are replaced by `libBugSplatMonitor.so` and the presigned upload, with an in-app prompt, ANR import and JVM exception reports as structured reports.
* `BugSplat.init(...)` keeps its role; user, email, description, attributes and attachments become properties settable at any time; the `BugSplatBridge` JNI names are kept for NDK code.
* Symbols: unchanged (`.sym` from your unstripped `.so` files with `symbol-upload -m`).

### What you do not have to change

Your database, application names and versions, symbol uploads, support responses, alerts and defect-tracker integrations, attribute searches: the server sees the same reports with a few new fields (`environment`, the client-side `crashHash`).
