# Migrating Unity from 4.x

### Initialization

Version 5.0.0 changes how BugSplat starts. Nothing needs to be in a scene anymore:

* **`BugSplatManager` is obsolete.** BugSplat initializes itself from the `BugSplatOptions` asset selected in **Edit > Project Settings > BugSplat**, before the first scene loads. Open that page — a project with exactly one options asset has it selected already — and remove the `BugSplatManager` from your scene. Until you do, it keeps working: it adopts the instance created at startup and logs a warning that it is no longer needed. **Capture Exceptions On Background Threads** and **Capture Unobserved Task Exceptions** moved from the manager to the options asset.
* **Replace `FindAnyObjectByType<BugSplatManager>().BugSplat` with `BugSplat.Instance`.** It is set before the first scene loads, so it is safe to read in any `Awake`.
* **Builds fail when no options asset is selected, or the selected one has an empty database.** A misconfigured project used to build a player that silently reported nothing. A development build only warns. If your code calls `BugSplat.Initialize` itself, select an asset with **Initialize Automatically** off.
* `BugSplatRef` has been removed.
* **Your existing options asset needs no edits.** `Enabled`, `Initialize Automatically`, and the three capture options are new fields, and an asset written before they existed loads with all of them **on** — so reporting keeps working exactly as it did. `Post Exceptions In Editor` stays off, as before.
* **Native crash reporting is now on by default for new assets** on Windows, macOS, iOS, and Android. An existing asset keeps the values saved in it, with one exception: `UseNativeCrashReportingForWindows` is new in 5.0, so an asset from 4.x turns it **on**. Uncheck it if you do not want `BugSplatMonitor` and the crash dialog in your Windows player.
* Not ready to configure BugSplat, or want it only in QA and release builds? Uncheck **Enabled** on the asset, or define `BUGSPLAT_DISABLED` for the targets you want it off for.

### Native Crash Reporting

Version 5.0.0 replaces the Unity crash-folder minidump flow with native crash reporting:

* `PostAllCrashes`, `PostCrash`, and `PostMostRecentCrash` have been removed. Unsent native crash reports are uploaded automatically at startup — you no longer need to call anything at launch. Delete any calls to these methods.
* Unity's `CrashReporting.crashReportFolder` minidumps are no longer read or uploaded. Windows native crashes are captured by [BugSplat for Windows](windows.md) instead.
* `Post(FileInfo minidump)` still works for posting your own minidump files on every platform except WebGL.

### Symbol Upload Credentials

* `SymbolUploadClientId` and `SymbolUploadClientSecret` have been removed from `BugSplatOptions`. Storing them there put the secret in version control and inside shipped builds. Set them per database from **BugSplat > Symbol Upload > Set Credentials**, or with environment variables in CI.
* Those environment variables are renamed from `BUGSPLAT_CLIENT_ID`/`BUGSPLAT_CLIENT_SECRET` to `SYMBOL_UPLOAD_CLIENT_ID`/`SYMBOL_UPLOAD_CLIENT_SECRET`. The old names are no longer read. See [Symbol Upload](symbol-upload.md).

{% hint style="danger" %}
**iOS projects exported with Append, or checked into version control, keep their old "Upload dSYM files to BugSplat" build phase.** Delete the old phase and build again, or re-export with Replace. A phase generated before 5.0.0 contains your Client ID and Secret in plain text — **rotate them**. Likewise, if an options asset holding credentials has ever been committed, rotate them.
{% endhint %}

### The Sample

**Delete any sample you imported under 4.x, then re-import it.** Package Manager copies samples into `Assets/` under a version-stamped folder, so an old copy stays in your project after the upgrade and keeps getting compiled. The 4.x sample calls methods 5.0.0 removed, so it will fail to compile until you delete it. Remove `Assets/Samples/BugSplat/<old version>/` and import the sample again from Package Manager. Deleting that folder discards everything in it, so copy out anything you changed and want to keep first — most often the `BugSplatOptions` asset, but the scene and scripts too if you edited them.

### Other Changes

These are also worth knowing; the package's [changelog](https://github.com/BugSplat-Git/bugsplat-unity/blob/main/CHANGELOG.md) has the full list:

* `BugSplatOptions.Attributes` is now a list of name/value pairs instead of a dictionary, so it can be edited in the inspector. Unity drops the old serialized value when a 4.x options asset is opened.
* Coroutines that `yield return bugsplat.Post(...)` now genuinely wait for the upload to finish.
* Post callbacks now run on the main thread, so they can safely call Unity APIs.
