# What is BugSplatReporter.exe?

If you've spotted `BugSplatReporter.exe`, `BugSplatMonitor.exe` or `BsSndRpt.exe` on your computer — in a folder, in Task Manager, or named in a message on screen — it's part of BugSplat, a crash reporting tool. It came with a program you already have installed, and it's trusted by thousands of developers to help them fix their bugs.

A crash reporter is a piece of code that helps developers find when, where, and how frequently their software crashes while in use. If you saw a BugSplat after your software shut down unexpectedly, it means that the developers behind your software are dedicated to its quality. And that's an excellent thing.

### Which file is which

| File | What it does |
| --- | --- |
| `BugSplatReporter.exe` | Shows the crash dialog that asks what you were doing, and sends the report. |
| `BugSplatMonitor.exe` | Runs quietly alongside the program and collects information about the crash. |
| `BugSplat.dll`, `BugSplatWer.dll` | Supporting components loaded by the program itself. |
| `BsSndRpt.exe` | The older name for the crash reporting program. You'll only see it alongside software built with an earlier version of BugSplat. |

These files are part of whichever program you were using when it crashed, so uninstalling them on their own isn't an option — and doing so would only stop that program's developers from hearing about problems you run into.

### My software keeps crashing

The best way to fix a program that keeps crashing is to work through the suggestions in our [Common End-User Questions](./) guide, or to contact the makers of your software program directly. BugSplat delivers crash reports to those developers; we're not able to fix or support their software ourselves.
