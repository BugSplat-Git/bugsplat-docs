---
description: Upgrading BugSplat for Windows to 9.0.0, and from versions prior to 7.0.0
---

# BugSplat for Windows Upgrade Guide

This guide covers the two releases of the BugSplat Windows/Xbox SDK that change what you ship or how you integrate it: [9.0.0](#upgrading-to-9.0.0), which moved the crash dialog into its own process, and [7.0.0](#upgrading-from-versions-prior-to-7.0.0), a significant upgrade of the whole SDK. If you're coming from a version before 7.0.0, read both.

## Upgrading to 9.0.0

In BugSplat for Windows 9.0.0, the crash dialog, the progress window and the upload moved out of `BugSplatMonitor.exe` into a new program, `BugSplatReporter.exe`. The dialog has a new design, and is customized with a `theme` folder of JSON files instead of by rebuilding `BugSplatRc.dll`, which no longer exists. See [How the Windows Crash Reporter Works](how-the-windows-crash-reporter-works.md) for how the pieces fit together.

{% hint style="danger" %}
**Add `BugSplatReporter.exe` to your installer.** Forgetting it doesn't produce an error: `BugSplatMonitor.exe` uploads the report itself when the reporter is missing, so crashes keep arriving, but **the crash dialog silently stops appearing**. Nobody is asked what they were doing, no support response is shown, and nobody can decline to send. The only sign is a line in the crash folder's `BugSplat.log`:

```
BugSplatReporter.exe not found next to BugSplatMonitor.exe - uploading in-process
without the crash dialog. Add BugSplatReporter.exe to your installer.
```

After upgrading, force a crash on a machine that installed your application from your installer, not from your build output, and confirm the dialog appears.
{% endhint %}

### Update Your Installer

Ship these files from the SDK's `BugSplat\<platform>\<config>\bin` folder, in the same folder as your executable:

| File | Change in 9.0.0 |
| --- | --- |
| `BugSplatReporter.exe` | ➕ **New.** Shows the crash dialog and uploads the report. |
| `BugSplatMonitor.exe` | Updated. Captures the crash, then starts `BugSplatReporter.exe`. |
| `BugSplatWer.dll` | Updated. |
| `BugSplat.dll` | Updated. Only if you link the dynamic library. |
| `theme\` | ➕ **New, optional.** `theme.json` and `strings.en-US.json`. Ship it if you customize the dialog. |
| `BugSplatRc.dll` | ➖ **Removed.** Delete it from your installer, your copy scripts and post-build steps, and your packaged application. Nothing loads it. |

The [`BugSplat`](https://www.nuget.org/packages/BugSplat) NuGet package copies `BugSplatReporter.exe` to your output with the rest of the native runtime, so a .NET application needs no project changes. If you copy BugSplat's files yourself, for example next to a native host's executable, copy `BugSplatReporter.exe` too. See [BugSplat for .NET](../bugsplat-for-dot-net.md).

Xbox titles are unaffected: there's no crash dialog on Xbox, so there's no `BugSplatReporter.exe` to ship.

### Update Every BugSplat Binary Together

The memory that your application and `BugSplatMonitor.exe` share changed in 9.0.0. Update `BugSplat.lib` or `BugSplat.dll`, `BugSplatMonitor.exe`, and `BugSplatWer.dll` together, all from the same release. A mixed set is detected and refused, so crashes aren't reported until every file matches.

### Move Dialog Customizations to the Theme

If you customized the dialog by editing `BugSplatRc.rc` and rebuilding `BugSplatRc.dll`, re-create those changes in the `theme` folder: colours, fonts, layout, logo and which fields appear in `theme.json`, and wording in `strings.en-US.json`. Nothing needs to be compiled, and `BugSplatReporter.exe --preview` shows the result without crashing anything. The defaults have changed too: the dialog has new wording, a new logo, and a light and a dark design that follows the user's Windows setting. See [Crash Dialog Branding](../../../../../education/how-tos/customize-the-crash-dialog.md). If your changes went further than a theme can, such as a different layout, the crash dialog's source is available to Enterprise customers.

### Allow Links in the Dialog, If You Want Them

The dialog's body text and contact note can now link to your privacy policy or support site. Links are off by default: they work only for domains your application allows in code with `SetCrashDialogLinkDomains` (`BugSplat_SetCrashDialogLinkDomains` in C, `CrashDialogLinkDomains` in .NET), so a theme file can't add a link on its own. See [Links in the Dialog](../../../../../education/how-tos/customize-the-crash-dialog.md#links-in-the-dialog).

Apart from that optional call, no code changes are required.

## Upgrading From Versions Prior to 7.0.0

The BugSplat Windows/Xbox SDK underwent a significant upgrade in version 7.0.0. Customers upgrading from earlier versions can use this section to assist their migration.&#x20;

### Architecture Changes

BsSndRpt.exe has been replaced by BugSplatMonitor.exe. When BugSplat is initialized, `BugSplatMonitor.exe` is launched and is responsible for generating minidump files from the primary application process. Since 9.0.0, the monitor then starts `BugSplatReporter.exe`, which displays a dialog prompting the user for additional information related to a crash, and sends the report to BugSplat.

BugSplat now integrates with the local WER service to capture crashes that cannot be caught by application code.  Crashes that can only be handled via a WER callback include fast-fail errors and certain types of memory overwrites. A new module, `BugSplatWer.dll`, must be installed with your application and configured in the registry to enable WER integration. Note that when WER handles an exception, there is no opportunity for application code to participate in the crash report. As a result, code in the Global Exception Filter is not executed.

### Code Changes

#### **BugSplat Constructor**

The BugSplat constructor initialization flags have been removed. Options can be configured after the BugSplat instance is initialized. Here are the new methods for configuring these options.

<table><thead><tr><th width="272.421875">Flag</th><th>New Method or Behavior</th></tr></thead><tbody><tr><td>MDSF_USEGUARDMEMORY</td><td><code>g_bugSplat.AllocGuardMemory(guardMemorySizeInBytes);</code> A 3-MB guard buffer is allocated by default.</td></tr><tr><td>MDSF_NONINTERACTIVE</td><td><code>g_BugSplat.SetQuietMode(true);</code></td></tr><tr><td>MDSF_FORCEEXIT</td><td>BugSplat's global exception filter always exits. Provide your own global exception filter to override.</td></tr><tr><td>MDSF_PREVENTHIJACKING</td><td>BugSplat now always attempts to keep our exception filter in place.</td></tr><tr><td>MDSF_DETECTHANGS</td><td><code>g_bugsplat.SetHangDetectionTimeout(detectionSeconds);</code> with detectionSeconds set to 0 to disable.</td></tr><tr><td>MDSF_SUSPENDALLTHREADS</td><td>No longer applicable since minidumps are now created by the external BugSplatMonitor process.</td></tr><tr><td>MDSF_LOGCONSOLE, MDSF_LOGFILE, MDSF_LOG_VERBOSE</td><td>BugSplat now always provides log files, and there is no longer a verbose option.</td></tr></tbody></table>

#### **Function Name Changes**

Several of the BugSplat method names have been changed:

| Old Method Name           | New Method Name    |
| ------------------------- | ------------------ |
| setDefaultUserName        | SetUser             |
| setDefaultUserEmail       | SetEmail           |
| setDefaultUserDescription | SetUserDescription |
| setNotes                  | SetNotes           |
| setAttribute              | SetAttribute       |
| createAsanReport          | CreateAsanReport   |
| createReport              | CreateXmlReport    |
| sendAdditionalFile        | AddAttachment      |

#### **Obsolete Functions**

`g_bugSplat.setCallback` The callback function has been removed. If you wish to perform tasks at crash time, provide your own global exception filter function. An example of this can be found in the MyConsoleCrasher example program.

### Build Changes

Add the `/GS` compiler flag to both Debug and Release configurations to enable checking stack overrun conditions.

Link with `BugSplat.lib` .

Copy `BugSplatMonitor.exe`, `BugSplatReporter.exe`, and `BugSplatWer.dll` to your executable folder.

### Installer Changes

#### **Redistributable Files**

Your installer must install `BugSplatMonitor.exe`, `BugSplatReporter.exe`, and `BugSplatWer.dll`, plus `BugSplat.dll` if you link the dynamic library. There is no longer a `BsSndRpt.exe` to install, and since 9.0.0 there's no `BugSplatRc.dll` either. These files should all be located in the same directory as your primary executable. See [Update Your Installer](#update-your-installer) for the optional `theme` folder.

#### **Registry Changes**

To enable BugSplat to register for WER callbacks, register the `BugSplatWer.dll` module as shown below.  This registry entry should be a `DWORD` whose name is the path to the `BugSplatWer.dll` file installed in the same location as your executable. The value should be `0`.

Most applications should create the WER registry key at the following location:

```
Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting\RuntimeExceptionHelperModules
```

However, 32-bit applications must create the WER registry key at a different location:

```
Computer\HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\Windows\Windows Error Reporting\RuntimeExceptionHelperModules
```

Here is that value shown highlighted in the registry editor for one of our sample applications:

<figure><img src="../../../../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

By default, WER will stop creating crash dumps after it has seen a few of the same type. To disable this, create the following registry key:&#x20;

```
Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps\{Application Name}
```

Add the following values under the new key you created:

<table><thead><tr><th width="197.11328125">Registry Key Setting</th><th width="358.19921875">Description</th><th>Value</th></tr></thead><tbody><tr><td>DumpType</td><td>Sets the type of dump to create.</td><td>0</td></tr><tr><td>DumpCount</td><td>Sets the maximum number of dumps.</td><td>0</td></tr></tbody></table>

Here's an example of this shown in the registry editor:

<figure><img src="../../../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

For additional information, see [https://learn.microsoft.com/en-us/windows/win32/wer/wer-settings](https://learn.microsoft.com/en-us/windows/win32/wer/wer-settings)

