---
description: >-
  Crash reporting for Unity 7's CoreCLR scripting backend: native crashes, C#
  exceptions, and hangs with C# function names, file names, and line numbers.
---

# Unity CoreCLR Crash Reporting

BugSplat supports Unity's CoreCLR scripting backend, the modern .NET runtime introduced in Unity 7. On Windows, BugSplat for Unity 5.2 and later reports native crashes, C# exceptions, hangs, and Windows Error Reporting crashes from CoreCLR players, and shows the C# frames in native crash reports with their function names, file names, and line numbers.

Nothing CoreCLR-specific needs to be set up. Install the package as described in [Unity](../unity.md), build your game with the CoreCLR backend, and BugSplat's Windows post-build step uploads everything the server needs.

### What BugSplat Captures

| Report                                  | Windows x64 and x86 |
| --------------------------------------- | ------------------- |
| C# exceptions                           | Yes                 |
| Native crashes, including under C# code | Yes                 |
| Stack overflows in C#                   | Yes                 |
| Hangs (opt-in)                          | Yes                 |
| Windows Error Reporting (fail-fast)     | Yes                 |

* **Native crashes under C# code**, such as an access violation in a P/Invoke call, are reported by BugSplat. Unity's CoreCLR runtime treats these as fatal errors and would otherwise send them to Unity's own crash handler.
* **Hangs** and **Windows Error Reporting** are configured the same way as for Mono and IL2CPP players. See [Windows Hang Detection](windows.md#windows-hang-detection) and [Windows Error Reporting](windows.md#windows-error-reporting).

### C# File Names and Line Numbers

In native crash reports, BugSplat names your C# frames and adds their file names and line numbers from your scripts' PDB files. The Windows post-build step uploads those PDBs at any Unity setting:

* Unity puts your scripts' PDBs in the build only when **Edit > Project Settings > Player > Other Settings > Managed Stack Trace Information** is set to **Method Name, File Name, and Line Number**. BugSplat then uploads them from the build folder.
* At the default, **Method Name**, the build has no PDBs. BugSplat uploads them from your project's `Library/Bee` folder instead, where the compiler wrote them, so nothing extra ships with your game.

C# exception reports are different: their stack traces come from the running game, so they include file names and line numbers only when the PDBs ship with it. If you want line numbers in C# exception reports too, set **Managed Stack Trace Information** to **Method Name, File Name, and Line Number**. Unity notes that this can add a few megabytes to your build.

### How C# Frames Get Their Names

CoreCLR compiles your C# code to machine code while the game runs. That code isn't in any file on disk, so a crash dump alone shows those frames as bare addresses, and uploading more symbol files can't help.

When a CoreCLR player crashes, BugSplat's crash handler adds a small amount of the runtime's own data to the crash report, about 0.2 MB. It's the data the runtime uses to map its compiled code back to C# methods. BugSplat's servers read it to name each C# frame, then use your uploaded assemblies and PDBs for file names and line numbers. Windows already does this for .NET apps built on Microsoft's runtime, but Unity builds its own copy of the runtime, so BugSplat collects the data itself.

### Coming From IL2CPP or Mono

* **IL2CPP:** CoreCLR builds don't use `LineNumberMappings.json`. The IL2CPP stack trace setting you may have set for BugSplat is named **Managed Stack Trace Information** in Unity 7, and CoreCLR builds don't need it for native crash reports.
* **Mono:** C# frames in Mono native crash reports are addresses. CoreCLR crash reports name them.

### Troubleshooting

**C# frames show as addresses, like `0x00007ffd2aa2efe6`.** Check that:

* The crash came from a CoreCLR player built with BugSplat for Unity 5.2 or later.
* The build's symbols were uploaded. The build log shows `uploading N script PDBs from Library/Bee` and a successful symbol upload. Symbol upload needs [credentials](symbol-upload.md).
* The `.dll` files in the build's `Managed` folder were uploaded with that version. Rebuilding changes them, so upload symbols from the same build you ship.

**C# frames have names but no file names or line numbers.** The script PDBs for that build weren't uploaded. Look for the `script PDBs` line in the build log, and check that the build was made in the same project folder it was uploaded from.
