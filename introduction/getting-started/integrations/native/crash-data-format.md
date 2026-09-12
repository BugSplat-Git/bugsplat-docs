---
description: >-
  Where BugSplat Native stores reports on the machine, what BugSplatCrashData.json
  contains, and how pending reports, retries and preferences work.
---

# Crash Data Format

BugSplat Native keeps every report as a folder on disk until it is uploaded or discarded. The folder is the contract between the library, the monitor and the reporter, and it is what you get from `bugsplat_pending_reports()`. The complete field-by-field reference is [CRASH-DATA-FORMAT.md](https://github.com/BugSplat-Git/bugsplat-native/blob/main/docs/CRASH-DATA-FORMAT.md) in the repository; this page is the tour.

### Where the store is

| Platform | Store directory |
| --- | --- |
| Windows | `%LOCALAPPDATA%\BugSplat\<app>-<version>\` |
| macOS | `~/Library/Application Support/BugSplat/<app>-<version>/` |
| Linux | `$XDG_STATE_HOME/bugsplat/<app>-<version>/` (default `~/.local/state/...`) |
| Android, iOS | the app's private files / Application Support directory, under `bugsplat/<app>-<version>/` |

`bugsplat_options_set_store_dir` overrides it; `bugsplat_log_file_path()` returns the `BugSplat.log` inside it, the first file support will ask for.

### Layout

```
<store>/
  BugSplat.log                          the SDK's log for this app and version
  preferences.json                      alwaysSend, persistedUser, persistedEmail (replaces the 8.x registry keys)
  sessions/<pid>-<start>.json           the attachment list of a running process
  <report id>/
    BugSplatCrashData.json              the report's metadata (schema 2)
    <report id>.dmp                     the dump; or bsCrashReport.xml / .json, bsAsanReport.xml, feedback.json
    <attachments>
    BugSplat.log                        what happened to this report
    result.json                         written once someone tried to upload it
```

### `BugSplatCrashData.json`

The report's identity (`database`, `appName`, `appVersion`, `crashTypeId`), the first-class properties (`key`, `user`, `email`, `userDescription`, `notes`, `environment`, `attributes`), what was captured (`reportKind`: `crash`, `hang`, `capture`, `structured`, `feedback`; `dumpFile`, `dumpType`: `normal`/`heap`/`full`; `dumpFormat`; `attachments`; `crashTime`; `signalOrExceptionCode`; `hangDurationMs`; `platform`, `arch`, `osVersion`), the client-side `crashSignature` and `crashSignatureHash`, and how the reporter should behave (`uploadPolicy`: `dialog`/`quiet`/`manual`, `openSupportUrl`, `themeDir`). Readers tolerate missing keys, so a report written by a newer SDK is never lost to an older reporter.

```json
{
  "schemaVersion": 2,
  "database": "fred", "appName": "MyApp", "appVersion": "1.0.0", "crashTypeId": "1",
  "user": "ada@example.com", "email": "", "key": "level-3", "userDescription": "", "notes": "",
  "environment": "Windows 11 10.0.26200 x64",
  "attributes": { "branch": "main" },
  "reportKind": "crash", "dumpFile": "3f2c9d1e-....dmp", "dumpType": "heap", "dumpFormat": "minidump",
  "attachments": ["app.log"],
  "crashTime": "2026-09-11T22:10:31Z", "signalOrExceptionCode": "0xc0000005",
  "crashSignature": "myapp.exe:0x1215|myapp.exe:0x14cf",
  "crashSignatureHash": "1db7fa4b...",
  "uploadPolicy": "dialog", "openSupportUrl": true
}
```

### `result.json`

Written by whoever uploaded (the reporter, or your call to `bugsplat_send_report`): `status` (`uploaded`, `cancelled`, `failed`, `rejected`, `deferred`), `errorCode`, `httpStatus`, `crashId`, `stackKeyId`, `infoUrl`, `cancelled`. Once it exists the report is no longer pending.

### Pending reports and retries

A report is pending when it has metadata, no `result.json`, and no live process working on it: a crash whose upload was interrupted, a report left by the `MANUAL` policy, a machine that was offline. On the next launch the monitor and the SDK retry; `bugsplat_pending_reports()` lists them for your own UI, `bugsplat_send_report()` uploads one (optionally with a new user, email or description), `bugsplat_discard_report()` deletes one, `bugsplat_post_pending_reports_async()` drains them in the background.

Retry policy: an upload that succeeded, a permanent refusal (size limit, rate limit, 4xx) or three failed attempts delete the folder; a server error (500, 502, 503, 504) keeps it for the next launch.

### What is uploaded

The dump or report file, the attachments and the report's log, zipped; the metadata travels as the fields of the commit call (`appKey`, `user`, `email`, `description`, `notes`, `attributes`, `environment`, `crashSignature`, `crashHash`). `BugSplatCrashData.json` and `result.json` never leave the machine. Protocol details: [Crash Post Endpoints](../../../development/web-services/crash.md).
