---
description: >-
  BugSplat Native 9.0 on Android: at-crash out-of-process capture with
  libBugSplatMonitor.so, an in-app prompt on next launch, ANR import, Kotlin API.
---

# Android

{% hint style="warning" %}
**Status: bring-up.** The Android backend and the Kotlin binding (bugsplat-android 9.0) are being built on the shared core. For production today use [BugSplat for Android](../mobile/android.md) (bugsplat-android 1.x), which keeps working against the server until you upgrade. Everything on this page is the shipping design; API names are final at the C level and may still change in Kotlin.
{% endhint %}

### How capture works on Android 🤖

Android does not allow a long-running helper process, so Crashpad's at-crash model is used: `libBugSplatMonitor.so` ships inside your APK as a native library and is executed as the monitor **at the moment of the crash**, from the crashing process, over a socket pair. It writes the dump, computes the signature and imports the report into the app's store; there is no dialog at crash time. On the next launch the SDK drains the store: with the `DIALOG` policy the app shows a Material prompt (Send / Don't Send / Always Send, name, email, description), with `QUIET` it uploads in the background, with `MANUAL` your code decides.

Java and Kotlin exceptions are handled by an `UncaughtExceptionHandler` that posts a [structured report](structured-reports.md) with the JVM stack; native (JNI/NDK) crashes produce minidumps. ANRs are imported from `ApplicationExitInfo` on the next launch as crash type 37.

### What ships in the APK 📦

* `libbugsplat.so` and `libBugSplatMonitor.so` for each ABI you ship (`arm64-v8a`, `armeabi-v7a`, `x86_64`), 16 KB page-aligned.
* The Kotlin binding `com.bugsplat:bugsplat-android:9.x` on Maven Central bundles both and the prompt UI.

### Initialize (Kotlin, bugsplat-android 9.0) 🏗️

```kotlin
BugSplat.init(context, BugSplatOptions(database = "fred", application = "MyApp", version = "1.0.0")
    .uploadPolicy(UploadPolicy.Dialog)
    .hangDetection(timeoutMs = 5000))
BugSplat.user = "ada@example.com"
BugSplat.setAttribute("branch", "main")
BugSplat.addAttachment(File(filesDir, "app.log"))
```

The C API is available to NDK code through `bugsplat/bugsplat.h` when the binding has initialized (or directly, passing the app's files directory as the store).

### What is different on Android 📱

* The **environment** string includes the API level and the device: `Android 14 (API 34) arm64-v8a; Google Pixel 8`.
* **Hang detection** pings the main `Looper`; a message that is not processed within the timeout is a hang (the ANR the OS would eventually report, caught earlier and with a full dump).
* The **support response** is not opened automatically (`open_support_url` defaults to off); the prompt exposes `infoUrl` so your app can show it.
* Heap and full memory dumps are not available (`BUGSPLAT_CAP_FULL_MEMORY_DUMP` is 0).

### Symbols 🔣

Native crashes are symbolicated from Breakpad `.sym` files generated from your unstripped `.so` files (Gradle keeps them under `build/intermediates/merged_native_libs` or your CMake build directory):

```bash
symbol-upload-linux -b your-database -a MyApp -v 1.0.0 -i your-client-id -s your-client-secret -d app/build -f "**/*.so" -m
```

JVM stacks in structured reports need no symbols; keep mapping files if you obfuscate with R8 so BugSplat can de-obfuscate them (upload as today, see [Android](../mobile/android.md#symbol-upload)).

### Where things are 🔍

Reports and `BugSplat.log`: `<filesDir>/bugsplat/<app>-<version>/` inside the app's private storage.
