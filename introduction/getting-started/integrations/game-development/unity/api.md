# Unity Options Reference

### Initialization

BugSplat initializes itself from the `BugSplatOptions` asset selected in **Edit > Project Settings > BugSplat**, before the first scene loads. Nothing needs to be placed in a scene.

| Member                                 | Description |
| -------------------------------------- | ----------- |
| `BugSplat.Instance`                    | The live client, or `null` before initialization. Set before the first scene loads when **Initialize Automatically** is on, so it is ready inside any `Awake`. |
| `BugSplat.IsInitialized`               | Whether `Instance` is set. |
| `BugSplat.Initialize(BugSplatOptions)` | Initializes from the given options and returns the client. For projects that turn **Initialize Automatically** off — to wait for a consent screen, say. Calling it again logs a warning and returns the existing instance. |
| `BUGSPLAT_MANUAL_INITIALIZE`           | Scripting define. The project owns initialization: BugSplat does not initialize itself, does not warn at startup, and does not fail a build for a missing asset. See [Initialize from Code Only](automation.md#initialize-from-code-only). |
| `BUGSPLAT_DISABLED`                    | Scripting define. BugSplat is off for that build target: no initialization, no build validation. The per-build-target form of the `Enabled` option. |

#### Turning BugSplat On and Off

Two independent questions, each answerable on the asset or with a define:

|                               | On the asset                           | As a define                  | Effect |
| ----------------------------- | -------------------------------------- | ---------------------------- | ------ |
| **Does BugSplat run at all?** | `Enabled` (default on)                 | `BUGSPLAT_DISABLED`          | Off: no initialization, and builds are not validated. An explicit `BugSplat.Initialize` call is still honored. |
| **Who starts it?**            | `InitializeAutomatically` (default on) | `BUGSPLAT_MANUAL_INITIALIZE` | Off: BugSplat does not start itself; your code calls `BugSplat.Initialize`. The database is still validated, because you still intend to report. |

Use the asset options for a project-wide choice and the defines for a per-build-target one — a build script that leaves `BUGSPLAT_DISABLED` on for development and off for QA and release, for example. The defines are set under **Edit > Project Settings > Player > Scripting Define Symbols**, or with `PlayerSettings.SetScriptingDefineSymbols`.

{% hint style="info" %}
A misconfiguration — no asset selected, or an empty database — **fails a release build** and only **warns on a development build**. Iterating on something unrelated never needs BugSplat filled in first, and a release player that silently reports nothing cannot slip through.
{% endhint %}

{% hint style="info" %}
`BugSplatManager` is obsolete. A 4.x scene that still has one keeps working, but it is no longer needed — see [Migrating from 4.x](migrating-from-4x.md).
{% endhint %}

### BugSplat Options

Every option below is a field on the `BugSplatOptions` asset, edited in **Edit > Project Settings > BugSplat**.

#### BugSplat Account

| Option        | Description |
| ------------- | ----------- |
| `Database`    | The name of a database in your BugSplat account. Required. |
| `Application` | The name of your BugSplat application. Defaults to `Application.productName` if no value is set. |
| `Version`     | The version of your BugSplat application. Defaults to `Application.version` if no value is set. |

#### Initialization

| Option                                 | Description |
| -------------------------------------- | ----------- |
| `Enabled`                              | Whether this project uses BugSplat at all. `true` by default. Off means it does not initialize and builds are not validated — see [Turning BugSplat On and Off](api.md#turning-bugsplat-on-and-off). |
| `InitializeAutomatically`              | Initialize BugSplat from this asset before the first scene loads. `true` by default. Turn it off to call `BugSplat.Initialize` yourself, for example after a consent screen. |
| `RegisterLogMessageReceived`           | Register a callback and report `LogType.Exception` log messages as they happen. `true` by default. |
| `CaptureExceptionsOnBackgroundThreads` | Also capture unhandled exceptions thrown on background threads. `true` by default. Requires `RegisterLogMessageReceived`. See [Background Thread Exceptions](../unity.md#background-thread-exceptions). |
| `CaptureUnobservedTaskExceptions`      | Also capture exceptions from Tasks that faulted and were never awaited. `true` by default. Requires `RegisterLogMessageReceived`. See [Unobserved Task Exceptions](../unity.md#unobserved-task-exceptions). |

#### Report Metadata

| Option        | Description |
| ------------- | ----------- |
| `Description` | A default description that can be overridden by a call to `Post`. |
| `Email`       | A default email that can be overridden by a call to `Post`. |
| `Key`         | A default key that can be overridden by a call to `Post`. |
| `Notes`       | A default general purpose field that can be overridden by a call to `Post`. |
| `User`        | A default user that can be overridden by a call to `Post`. |
| `Attributes`  | Name/value attributes to attach to reports. |

#### Capture

| Option                              | Description |
| ----------------------------------- | ----------- |
| `CaptureEditorLog`                  | Upload `Editor.log` when `Post` is called. |
| `CapturePlayerLog`                  | Upload `Player.log` when `Post` is called. `true` by default — see [Player Log and Privacy](api.md#player-log-and-privacy). |
| `LogFileMaxSizeMB`                  | Maximum size of the log files to upload, in MB. `10` by default. |
| `CaptureScreenshots`                | Take a screenshot and upload it when `Post` is called. |
| `PostExceptionsInEditor`            | Upload exceptions thrown in the editor. `false` by default, so play mode exceptions stay out of your database. |
| `PersistentDataFileAttachmentPaths` | Paths to files, relative to `Application.persistentDataPath`, to attach to managed reports, and to native crash reports on platforms where native crash reporting is enabled. |

`PersistentDataFileAttachmentPaths` entries are relative to `Application.persistentDataPath`, so write `logs/session.log`, not `/Users/you/Desktop/session.log`. An absolute path is skipped with a warning: a path from the machine that authored the options asset would not exist on a teammate's machine, in CI, or on a player's device.

#### Windows

| Option                              | Description |
| ----------------------------------- | ----------- |
| `UseNativeCrashReportingForWindows` | Capture native crashes with BugSplat for Windows, in addition to .NET exceptions. Works with both Mono and IL2CPP. `true` by default. |
| `UploadDebugSymbolsForWindows`      | Upload `.pdb`, `.dll`, and `.exe` symbols for Windows builds. `true` by default. Also requires **Copy PDB Files** in Build Settings — see [Windows Symbols](windows.md#windows-symbols). |
| `WindowsShowCrashDialog`            | Show the BugSplat crash dialog when a native crash occurs. `true` by default. When disabled, crash reports are sent silently. |
| `WindowsHangDetectionTimeoutMs`     | Hang detection timeout in milliseconds. `0` (default) disables hang detection. A detected hang is reported and the process is terminated — see [Windows Hang Detection](windows.md#windows-hang-detection). |

#### macOS

| Option                             | Description |
| ---------------------------------- | ----------- |
| `UseNativeCrashReportingForMac`    | Capture native crashes on macOS. `true` by default. Requires IL2CPP; a Mono player logs a warning and reports managed exceptions only. |
| `UploadDebugSymbolsForMac`         | Upload dSYMs (and `LineNumberMappings.json` for IL2CPP) for macOS builds. `false` by default. |
| `MacAutoSubmitCrashReport`         | Submit crash reports without asking the user. `false` by default, so a native crash shows the BugSplat dialog on the next launch. |
| `MacAutoSubmitFatalHangReport`     | Submit fatal hang reports without asking the user. `true` by default. Turning it off also requires `MacAutoSubmitCrashReport` to be off. |
| `MacHangDetectionThresholdSeconds` | Seconds the main thread must be blocked before BugSplat declares a hang. `5` by default. Positive values below 0.1 are clamped to 0.1; zero or less falls back to the SDK default with a warning. |

#### iOS

| Option                             | Description |
| ---------------------------------- | ----------- |
| `UseNativeCrashReportingForIos`    | Capture native crashes on iOS. `true` by default. |
| `UploadDebugSymbolsForIos`         | Add a build phase to the Xcode project that uploads dSYMs (and `LineNumberMappings.json` for IL2CPP). `false` by default. |
| `IosAutoSubmitCrashReport`         | Submit crash reports without asking the user. `true` by default. Turn it off to show the BugSplat dialog on the next launch instead. |
| `IosAutoSubmitFatalHangReport`     | Submit fatal hang reports without asking the user. `true` by default. Turning it off also requires `IosAutoSubmitCrashReport` to be off. |
| `IosHangDetectionThresholdSeconds` | Seconds the main thread must be blocked before BugSplat declares a hang. `5` by default. Positive values below 0.1 are clamped to 0.1; zero or less falls back to the SDK default with a warning. |

#### Android

| Option                              | Description |
| ----------------------------------- | ----------- |
| `UseNativeCrashReportingForAndroid` | Capture native crashes and ANRs on Android. `true` by default. |
| `UploadDebugSymbolsForAndroid`      | Upload the generated symbols archive after an Android build. `false` by default. |

{% hint style="info" %}
`ShouldPostException` is not a field on the options asset. It is a runtime-only property you assign on your `BugSplat` instance in code — see [Preventing Repeated Reports](../unity.md#preventing-repeated-reports).
{% endhint %}

### Player Log and Privacy

`CapturePlayerLog` is **enabled by default** — a new options asset and a `BugSplat` created in code both start with it on — because `Player.log` is the most useful attachment on a crash report. WebGL has no `Player.log`, so the setting has no effect there.

Unity writes `Player.log` under the user's profile directory on every desktop platform, and it records file system paths that contain the operating system username. If you would rather not collect that, uncheck **Capture Player Log** on your options asset, or set the property in code:

```csharp
bugsplat.CapturePlayerLog = false;
```

{% hint style="info" %}
**Upgrading from 4.x:** options assets created before 5.0.0 keep whatever value is already saved in them; only newly created assets pick up the new default. Check the field on your existing asset if you want the new behavior.
{% endhint %}

### Attaching Files to Native Crash Reports

`Attachments` adds files to managed reports only. A native crash is captured and uploaded by the platform's crash reporter, which never sees that list. Two things do reach native reports, both only when native crash reporting is enabled for the platform: `PersistentDataFileAttachmentPaths` on your options asset, which is applied to both mechanisms at startup, and `AttachNativeLogFile` in code:

```csharp
bugsplat.AttachNativeLogFile("/path/to/support.log");
bugsplat.DetachNativeLogFile("/path/to/support.log");
```

Attaching is **additive and idempotent**. Every attached file is included in a native report, attaching one file never displaces another, and attaching the same file twice attaches it once. Paths are resolved to full paths before they are compared — case-insensitively on Windows. `DetachNativeLogFile` removes one file and leaves the rest attached. Multiple native attachments are supported on Windows, macOS, iOS, and Android; on macOS and iOS each attachment is truncated to its last 10 MB.

{% hint style="warning" %}
**Register native attachments during initialization.** On macOS and iOS a crash report is uploaded at the *next* launch, and BugSplat asks for that report's attachments then, in a fresh process. A path registered part-way through a session is not remembered across the crash, so it never reaches the report. `PersistentDataFileAttachmentPaths` is applied on every launch and is unaffected by this. On Windows and Android the handler captures attachments at crash time, so `AttachNativeLogFile` takes effect whenever you call it.
{% endhint %}

`CapturePlayerLog` uses the same mechanism with `Application.consoleLogPath`, so the two cooperate: setting it `false` detaches only `Player.log`, and attaching your own file leaves `Player.log` alone. Prefer `CapturePlayerLog` for that file rather than attaching `Application.consoleLogPath` yourself.
