---
description: >-
  BugSplat Native 9.0 on iOS and tvOS: in-process Crashpad capture, reports sent
  on the next launch through the same store and upload as every other platform.
---

# iOS and tvOS

{% hint style="warning" %}
**Status: bring-up.** The iOS/tvOS backend and the Swift-first API (bugsplat-apple 9.0) are being built on the shared core; tvOS ships as a beta. For production today use [BugSplat for iOS](../mobile/ios.md) (bugsplat-apple 2.x). Everything on this page is the shipping design.
{% endhint %}

### How capture works on iOS 📱

iOS and tvOS do not allow a helper process, so this is the one place BugSplat Native captures **in process**: Crashpad's in-process handler writes an intermediate dump at crash time, and on the next launch the SDK converts it to a minidump, imports it into the store, computes the crash signature, and either prompts (`DIALOG`: a UIKit/SwiftUI alert with Send / Don't Send / Always Send) or uploads in the background (`QUIET`). The dump, the report store, the upload and the support response are the same as on every other platform; only the capture differs.

Exceptions caught by the Objective-C runtime (`NSException`) and Swift fatal errors are captured with their stacks; `EXC_BAD_ACCESS`, `abort`, stack overflows and signals produce minidumps. Mac Catalyst is not supported.

### What ships in your app 📦

`BugSplat.xcframework` (arm64 device, arm64 and x86-64 simulator, tvOS) through Swift Package Manager or as a manual embed; nothing else. There is no monitor or reporter on iOS.

### Initialize (Swift, bugsplat-apple 9.0) 🏗️

```swift
import BugSplat

BugSplat.start(database: "fred", application: "MyApp", version: "1.0.0") { options in
    options.uploadPolicy = .dialog
    options.hangDetection = .init(timeout: 3.0, policy: .report)
}
BugSplat.user = "ada@example.com"
BugSplat.setAttribute("branch", "main")
BugSplat.addAttachment(logURL)
```

The C API is available to C and C++ code in the same app through `bugsplat/bugsplat.h`.

### What is different on iOS 🍏

* **In process**: a crash inside the handler itself, or one that corrupts memory the handler needs, may not produce a report. This is the platform's constraint, not a policy choice; everywhere a helper process is allowed, BugSplat uses one.
* **Crash callback**: none on Apple platforms.
* **Hang detection** pings the main dispatch queue; `hangDetection` produces a non-fatal report with a full dump while the app keeps running.
* **Heap and full dumps** are not available; `dump_type` is ignored with a logged warning.
* The **support response** is not opened automatically; the prompt exposes `infoUrl`.
* The **environment** string names the device: `iOS 17.5 (21F79) arm64; iPhone15,3`.

### Symbols 🔣

iOS and tvOS reports are processed with Breakpad `.sym` files generated from your dSYMs (`dump_syms` runs inside `symbol-upload -m`):

```bash
symbol-upload-macos -b your-database -a MyApp -v 1.0.0 -i your-client-id -s your-client-secret -d "$DWARF_DSYM_FOLDER_PATH" -f "**/*.dSYM" -m
```

Keep Bitcode off so the dSYMs you build match the binary Apple distributes.

### Where things are 🔍

Reports and `BugSplat.log`: `<Application Support>/BugSplat/<app>-<version>/` in the app's container.
