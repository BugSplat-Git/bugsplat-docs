---
description: >-
  Report crashes from a Windows game protected by Easy Anti-Cheat by renaming
  the BugSplat monitor and telling BugSplat its new name.
---

# Easy Anti-Cheat

[Easy Anti-Cheat](https://www.easy.ac/) (EAC) stops other processes from reading a protected game's memory. BugSplat's monitor process has to read that memory to write a crash dump, so a protected game needs two small changes before BugSplat can report its crashes:

1. Ship `BugSplatMonitor.exe` under a file name that begins with `CrashReport`.
2. Tell BugSplat the new name before you initialize it.

Nothing else in your BugSplat integration changes.

{% hint style="info" %}
Easy Anti-Cheat support requires BugSplat for Windows 9.5.0 or later. Earlier releases open the game in a way Easy Anti-Cheat doesn't allow and can't be used with it.
{% endhint %}

## How it works

Easy Anti-Cheat makes an exception for an external crash reporter that follows two rules.

* **It doesn't open the game with memory access until there is a crash to dump.** BugSplat's monitor does this on its own. It watches the game with a handle that can only wait on it and query it, and opens the game for reading once the game reports a crash or a hang is detected.
* **Its file name begins with `CrashReport`.** This is the part you set up.

## Set up your game

### 1. Rename the monitor

In the folder you ship, rename `BugSplatMonitor.exe` to a name beginning with `CrashReport`, for example `CrashReportBugSplat.exe`. Leave `BugSplatReporter.exe` and `BugSplatWer.dll` as they are.

### 2. Tell BugSplat the new name

Call `SetMonitorExeName` before you create BugSplat, because creating it starts the monitor.

{% tabs %}
{% tab title="C++" %}
```cpp
BugSplat::SetMonitorExeName(L"CrashReportBugSplat.exe");
BugSplat bugsplat(L"my_database", L"MyGame", L"1.0.0");
```
{% endtab %}

{% tab title="C" %}
```c
BugSplat_SetMonitorExeName(L"CrashReportBugSplat.exe");
BugSplat_Init(L"my_database", L"MyGame", L"1.0.0");
```
{% endtab %}

{% tab title="C#" %}
```csharp
BugSplat.SetMonitorExeName("CrashReportBugSplat.exe");
var bugsplat = new BugSplat("my_database", "MyGame", "1.0.0");
```
{% endtab %}
{% endtabs %}

The name is a file name only, with no path. The call returns false, and changes nothing, if the name contains a path or the monitor has already been started.

### 3. Sign the game folder last

Easy Anti-Cheat checks your game's files against a catalog made by `anticheat_integritytool.exe`. Run the tool as the last step of your build, after the rename, so the catalog lists the monitor under its new name.

{% hint style="info" %}
Easy Anti-Cheat has a second way to allow a crash reporter: set `crash_reporter_name` in `anticheat_integritytool.cfg`, for example `crash_reporter_name = "BugSplatMonitor.exe";`. With that setting you don't rename the monitor or call `SetMonitorExeName`.
{% endhint %}

## What's different in a protected game

* **A crash report always ends the game.** Easy Anti-Cheat suspends every thread of the game when the monitor opens it to write a dump, and ends the game about a minute later. BugSplat doesn't wait for that: the monitor writes the dump, ends the frozen game, shows the crash dialog unless quiet mode is on, and uploads the report. This takes about two seconds. It also applies to reports for exceptions your code handles and recovers from: the first such report ends the game.
* **Crashes captured through Windows Error Reporting need no change.** Stack buffer overruns, fail-fast exceptions and similar crashes go through `BugSplatWer.dll` and are dumped by Windows' own `WerFault.exe`, which Easy Anti-Cheat already allows. `BugSplatWer.dll` must still be [registered](bugsplat-for-windows-upgrade-guide.md#registry-changes) by the full path it is installed to.
* **Debuggers can't attach** while the protection is active. Start the game without the Easy Anti-Cheat launcher to debug it.

## Check that it works

1. Start your game through the Easy Anti-Cheat launcher (`start_protected_game.exe`).
2. In Task Manager, check that your game, the renamed monitor and `EasyAntiCheat_EOS.exe` are all running, and that the game is still running after 15 seconds. A crash reporter Easy Anti-Cheat doesn't accept makes the game freeze and end soon after it starts.
3. Crash the game. It should close within a few seconds, and the report should appear on the [Crashes](https://app.bugsplat.com/v2/crashes) page.

## Troubleshooting

| Symptom | Likely cause |
| --- | --- |
| The game freezes and ends soon after starting, without crashing | The monitor's file name doesn't begin with `CrashReport`, and it isn't named in `crash_reporter_name` |
| The game crashes, nothing is reported, and no monitor process ran | `SetMonitorExeName` wasn't called, was called after BugSplat was created, or names a file that isn't in the game's folder |
| Easy Anti-Cheat reports an untrusted or modified file | The monitor was renamed, or a file changed, after the integrity tool ran. Run it again |
| The report doesn't reach BugSplat | Read `BugSplat.log` in the crash folder under `%TEMP%\BugSplat\<application>-<version>\`. A folder that is still there holds a report that wasn't sent, and is sent the next time the game starts |

For Easy Anti-Cheat's own setup, see Epic's [Anti-Cheat Interfaces](https://dev.epicgames.com/docs/epic-online-services/trust-and-safety/anti-cheat-interfaces/anti-cheat-interfaces) documentation.
