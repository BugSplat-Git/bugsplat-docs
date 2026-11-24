---
description: >-
  BugSplat Native 9.0 is one out-of-process crash reporter for Windows, macOS,
  Linux, Android, iOS and tvOS: the same dialog, upload path, support response
  and crash-report format on every platform.
---

# 🧬 BugSplat Native (9.0)

BugSplat Native is the successor to BugSplat for Windows 8.x, bugsplat-apple 2.x and bugsplat-android 1.x: one open-source SDK ([BugSplat-Git/bugsplat-native](https://github.com/BugSplat-Git/bugsplat-native), MIT) with a C API, a header-only C++ wrapper, and .NET, Swift and Kotlin bindings, built on Google's Crashpad.

{% hint style="info" %}
**Status.** Windows is complete and verified end to end. macOS, Linux, Android and iOS/tvOS build in CI and are being brought up platform by platform; each platform page carries a status line at the top. Until your platform's page says "verified", keep using the existing integration for production and evaluate 9.0 alongside it.
{% endhint %}

### How it works

Every desktop platform gets the same two helper processes next to your application:

| Component | Role |
| --- | --- |
| `BugSplatMonitor` | Watches your process from outside. When it crashes, writes the dump (normal, heap or full), computes the crash signature, copies your attachments, and hands the report to the reporter. Same name on every platform. |
| `BugSplatReporter` | The crash dialog (themeable, translatable) and the upload. Opens the support response when there is one. |
| `BugSplat.dll` / `libbugsplat.dylib` / `libbugsplat.so` | The library your app links: the C API, the report store, the uploader, structured reports, feedback, hang detection. |
| `BugSplatWer.dll` | Windows only: the Windows Error Reporting helper for fail-fast crashes. |

Capture, dump writing, the dialog and the upload all happen **out of your process**, so a crash that corrupts the heap, exhausts the stack or takes out the C runtime is still reported. On iOS and tvOS, where the OS forbids a helper process, capture is in process and the report is sent on the next launch.

Initialization refuses to run without the monitor and reporter (`BUGSPLAT_ERR_MONITOR_NOT_FOUND`, `BUGSPLAT_ERR_REPORTER_NOT_FOUND`): a packaging mistake shows up on the developer's machine, not as silently missing crashes in the field.

### Ten lines

```c
#include <bugsplat/bugsplat.h>

int main(void) {
  bugsplat_options* o = bugsplat_options_new("fred", "MyApp", "1.0.0");   /* database, app, version */
  bugsplat_init(o);                                    /* starts BugSplatMonitor out of process */
  bugsplat_set_user("fred@bugsplat.com");              /* every property can change at any time */
  bugsplat_set_attribute("branch", "main");
  bugsplat_add_attachment("app.log");
  volatile int* p = 0; *p = 42;                        /* dialog, upload, support response */
}
```

C++: `bugsplat::BugSplat bs("fred", "MyApp", "1.0.0"); bs.SetUser("fred");`\
.NET: `using var bs = new BugSplat("fred", "MyApp", "1.0.0"); bs.HandleApplicationExceptions();`

### What every platform shares

* **Report properties, changeable at any time**: key (selects the [support response](../../../production/setting-up-custom-support-responses.md)), user, email, description, notes, up to 64 attributes and up to 24 attachments. Whatever the values are at the instant of the crash is what the report carries.
* **Environment**: a new first-class property, detected automatically (`Windows 11 10.0.26200 x64`, `macOS 14.5 (23F79) arm64`, `Android 14 (API 34) arm64-v8a; Google Pixel 8`), overridable, shown on the crash page. It is how BugSplat tells platforms apart now that every Crashpad platform posts the same crash type.
* **Upload policies**: `DIALOG` (ask the user, then send), `QUIET` (send without UI), `MANUAL` (leave the report pending and let the app decide).
* **The presigned upload** every BugSplat platform uses, with a retry on the next launch for server errors.
* **Client-side crash signature** so repeat crashes group instantly and the support response resolves at once.
* [**Hang detection**](hang-detection.md), [**structured reports**](structured-reports.md) for engines and managed code, [**user feedback**](user-feedback.md), non-fatal captures, and heap/full memory dumps on every desktop platform.
* One [**crash-report store**](crash-data-format.md) format, so a pending report from any platform looks the same.

### Platforms

| Platform | Capture | Dialog | Guide |
| --- | --- | --- | --- |
| Windows (x64, x86, ARM64) | out of process; WER for fail-fast | `BugSplatReporter.exe`, Win32 | [Windows](windows.md) |
| macOS 13+ | out of process | `BugSplatReporter.app`, AppKit | [macOS](macos.md) |
| Linux (glibc 2.31+) | out of process | `BugSplatReporter`, GTK 3 or headless | [Linux](linux.md) |
| Android | out of process, at crash time | in-app prompt on next launch | [Android](android.md) |
| iOS, tvOS | in process (OS rule) | in-app prompt on next launch | [iOS and tvOS](ios.md) |
| Xbox | private bolt-on for certified developers | | [Xbox](../game-development/xbox.md) |

### Guides

{% content-ref url="hang-detection.md" %}
[hang-detection.md](hang-detection.md)
{% endcontent-ref %}

{% content-ref url="structured-reports.md" %}
[structured-reports.md](structured-reports.md)
{% endcontent-ref %}

{% content-ref url="user-feedback.md" %}
[user-feedback.md](user-feedback.md)
{% endcontent-ref %}

{% content-ref url="crash-data-format.md" %}
[crash-data-format.md](crash-data-format.md)
{% endcontent-ref %}

{% content-ref url="migration.md" %}
[migration.md](migration.md)
{% endcontent-ref %}

### Reference

The full API reference, the crash-data format, the upload protocol and the theme and strings formats live with the code:

* [API reference](https://github.com/BugSplat-Git/bugsplat-native/blob/main/docs/API.md)
* [Crash data format](https://github.com/BugSplat-Git/bugsplat-native/blob/main/docs/CRASH-DATA-FORMAT.md) and [upload protocol](https://github.com/BugSplat-Git/bugsplat-native/blob/main/docs/UPLOAD-PROTOCOL.md)
* [Theming the dialog](https://github.com/BugSplat-Git/bugsplat-native/blob/main/reporter/docs/THEME.md) and [translating it](https://github.com/BugSplat-Git/bugsplat-native/blob/main/reporter/docs/STRINGS.md)
* [Architecture](https://github.com/BugSplat-Git/bugsplat-native/blob/main/docs/ARCHITECTURE.md)
