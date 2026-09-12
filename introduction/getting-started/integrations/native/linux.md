---
description: >-
  Add BugSplat Native 9.0 to a Linux application: the monitor and reporter next
  to your binary, headless uploads, .sym symbols.
---

# Linux

{% hint style="warning" %}
**Status: bring-up.** The Linux build compiles and passes the unit tests in CI; the crash-through-monitor path and the GTK reporter are being verified on real distributions. For production today see the [Crashpad integration](../desktop/linux.md). Details marked *planned* are not yet in a release.
{% endhint %}

### Requirements 📋

* glibc 2.31 or later (Ubuntu 20.04, Debian 11, RHEL 9 and newer), x86-64 and aarch64.
* Runtime dependencies are loaded with `dlopen` and are optional: `libcurl.so.4` for uploads (present on every desktop distribution) and GTK 3 for the dialog. Without a display or GTK the reporter uploads headlessly, as if the policy were `QUIET`.
* CMake 3.24+ and GCC 11+ or Clang 14+ to build.

### What ships next to your binary 📦

| File | Purpose |
| --- | --- |
| `libbugsplat.so` | the library you link |
| `BugSplatMonitor` | out-of-process capture over a socket pair; no libcurl, no GTK |
| `BugSplatReporter` + `theme/` | the dialog (GTK 3, loaded at run time) and the upload |

```cmake
find_package(bugsplat CONFIG REQUIRED)
target_link_libraries(my_app PRIVATE bugsplat::bugsplat)
bugsplat_install_runtime(TARGET my_app)
```

Tarballs per architecture are published with each release (*planned*: `bugsplat-9.x-linux-x86_64.tar.gz`, `-aarch64`).

### Initialize 🏗️

```c
bugsplat_options* o = bugsplat_options_new("fred", "MyApp", "1.0.0");
bugsplat_options_set_upload_policy(o, BUGSPLAT_UPLOAD_DIALOG);   /* QUIET for servers and daemons */
bugsplat_options_set_hang_detection(o, 5000, BUGSPLAT_HANG_REPORT);
if (bugsplat_init(o) != BUGSPLAT_OK) { /* BugSplatMonitor / BugSplatReporter not next to the binary */ }
```

Report properties, attachments, feedback, structured reports and pending reports work as on [Windows](windows.md).

### What is different on Linux 🐧

* **Hang detection** has no built-in main-loop pinger (there is no one main loop on Linux): call `bugsplat_heartbeat()` from your main loop at least once per timeout, or leave detection off.
* **Hosts with their own signal handlers** (.NET, Mono, the JVM): `bugsplat_options_set_chain_previous_signal_handlers(o, 1)` lets the runtime's handler see the fault first, so managed null references stay managed and only real native faults produce dumps. `BugSplatDotNet` sets this for you.
* **`kill -SEGV`** and other externally delivered fatal signals are captured like any crash.
* **Support response** opens with `xdg-open` after an interactive upload; nothing opens in `QUIET` mode or without a display.
* **Heap and full dumps** are standard minidumps with memory regions appended (`lldb`, `minidump_stackwalk` read them).

### Symbols 🔣

Build with `-g` and link with `-Wl,--build-id` (the build id matches the module to its symbols); `-fno-omit-frame-pointer` on x86-64 improves the client-side crash signature. Upload the unstripped binaries or `.debug` files, converted to Breakpad `.sym`:

```bash
symbol-upload-linux -b your-database -a MyApp -v 1.0.0 -i your-client-id -s your-client-secret -d ./build -f "**/*.{so,debug}" -m
```

Ship stripped binaries; keep the unstripped ones for the upload.

### Where things are 🔍

Reports and `BugSplat.log`: `$XDG_STATE_HOME/bugsplat/<app>-<version>/`, i.e. `~/.local/state/bugsplat/<app>-<version>/` by default.

### Troubleshooting 🛠️

| Symptom | Cause |
| --- | --- |
| `BUGSPLAT_ERR_MONITOR_NOT_FOUND` | `BugSplatMonitor` is not next to the binary or `libbugsplat.so` |
| Reports stay pending, log says the upload failed at the transport level | `libcurl.so.4` is not installed, or no network; pending reports retry on the next launch |
| No dialog | no `DISPLAY`/`WAYLAND_DISPLAY`, or GTK 3 is not installed; the report was uploaded headlessly |
| Dumps but no useful stack for a .NET app | the runtime's handler and BugSplat's are competing; make sure signal-handler chaining is on |
