---
description: >-
  Detect main-thread hangs on every platform and report them as full dumps
  through the same pipeline as crashes, with non-fatal and fatal policies.
---

# Hang Detection

A hang is a crash the operating system never reports: the main thread stops making progress and the user eventually force-quits. BugSplat Native detects hangs the same way on every platform and reports them as an out-of-process dump of the whole process, tagged `reportKind=hang`, through the same store, dialog and upload as a crash.

### Enable

```c
bugsplat_options_set_hang_detection(o, 5000, BUGSPLAT_HANG_REPORT);   /* timeout in ms; 0 = off */
```

C++: `Options(...).HangDetection(5000)`. .NET: `Options { HangTimeoutMs = 5000 }`.

### How progress is observed

A watchdog thread in your process checks every quarter of the timeout (at least every 100 ms) how long ago the main thread last showed progress:

| Platform | What counts as progress |
| --- | --- |
| Windows | one of your visible top-level windows answered a `WM_NULL` sent with `SendMessageTimeout`, so its thread is pumping messages |
| macOS, iOS, tvOS | a block queued on the main dispatch queue ran, so the run loop turned |
| Android | the main `Looper` processed the ping (bugsplat-android 9.0) |
| Linux, services, console tools, game loops | you called `bugsplat_heartbeat()` |

Applications without a message loop or run loop, and engines that own their main loop, call `bugsplat_heartbeat()` from the loop they consider alive, at least once per timeout. Calling it when detection is off is harmless.

### Watching more threads

```c
/* on the render thread */
bugsplat_watch_thread("render");
for (;;) { render_frame(); bugsplat_heartbeat(); }
bugsplat_unwatch_thread();
```

Each watched thread is judged against the same timeout, and the report names the thread that stalled (`main`, `render`, ...).

### What a hang report looks like

The same minidump as a crash, with every thread's stack and the hung thread frozen where it is, plus `reportKind: hang` and the hang duration. The dialog shows the "not responding" copy (`hangTitle`, `hangHeadline`, `hangBody` in `strings.<lang>.json`) instead of the crash copy. Repeated hangs at the same place share a crash signature and group together, and at most one hang is reported per five minutes per process.

### Policies

| Policy | Behaviour |
| --- | --- |
| `BUGSPLAT_HANG_REPORT` (default) | non-fatal: report, keep running; the user may never notice |
| `BUGSPLAT_HANG_REPORT_AND_TERMINATE` | fatal: the dialog offers **Wait** and **Close**; on Close the monitor ends the application after the dump (the BugSplat for Windows 8.x behaviour). *Status: the Wait/Close step is being finished with the Windows monitor-side hang probe; until then this policy behaves like the non-fatal one and is recorded in the report.* |

### Choosing a timeout

Pick a value comfortably above the longest legitimate main-thread stall in your application (asset loading, large JSON parses, first-frame shader compilation): 2-5 seconds for interactive apps, 30 seconds or more for tools that block on purpose. A too-short timeout reports ordinary slowness as hangs; because reports are deduplicated and carry the duration, a false positive costs one report, not a storm. Breakpoints under a debugger look like hangs, so consider leaving detection off in debug builds.
