---
description: Upgrading from versions of BugSplat for Windows prior to 7.0.0
---

# BugSplat for Windows Upgrade Guide

The BugSplat Windows/Xbox SDK underwent a significant upgrade in version 7.0.0. Customers upgrading from earlier versions can use this guide to assist their migration.&#x20;

{% hint style="danger" %}
**Read [Update your installer](#update-your-installer) before you ship.** The crash dialog now lives in `BugSplatReporter.exe`, a **new file** that did not exist in earlier releases, and `BugSplatRc.dll` no longer exists at all. If you update the SDK binaries without updating your installer, crash reports keep arriving — but **the crash dialog silently stops appearing**, and your users are never asked what they were doing.
{% endhint %}

### Architecture Changes

BsSndRpt.exe has been replaced by BugSplatMonitor.exe. When BugSplat is initialized, `BugSplatMonitor.exe` is launched and is responsible for generating minidump files from the primary application process.

Once the crash is captured, the monitor launches `BugSplatReporter.exe --report <folder>`, which displays the dialog prompting the user for additional information and sends the report to BugSplat. Capture and reporting are separate processes: the monitor owns the minidump, WER integration and hang detection, and the reporter owns the dialog and the upload. See [How the Windows Crash Reporter Works](how-the-windows-crash-reporter-works.md) for the full flow.

Because the reporter reads its appearance from `theme\theme.json` and its text from `theme\strings.en-US.json` at runtime, `BugSplatRc.dll` — the resource-only DLL that used to hold the dialog templates and artwork — has been removed. Customizations that were made by editing `.rc` files and rebuilding that DLL do not carry forward and need to be re-expressed as JSON. See [Crash Dialog Branding](../../../../../education/how-tos/customize-the-crash-dialog.md).

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

#### **Update your installer**

Your installer must install `BugSplat.dll`, `BugSplatMonitor.exe`, `BugSplatReporter.exe`, and `BugSplatWer.dll`. These files should all be located in the same directory as your primary executable.

Relative to earlier releases, that means two changes:

| Change | File | Why |
| --- | --- | --- |
| ➕ **Add** | `BugSplatReporter.exe` | The crash dialog, the progress window and the upload moved here out of `BugSplatMonitor.exe`. |
| ➖ **Remove** | `BugSplatRc.dll` | The resource-only DLL is no longer built, and nothing loads it. The dialog's appearance and copy are now JSON, read at runtime. |
| ➖ **Remove** | `BsSndRpt.exe` | Replaced by `BugSplatMonitor.exe` in 7.0.0. |

Optionally also install the `theme` folder — `theme.json` and `strings.en-US.json` — next to `BugSplatReporter.exe`. It is only needed if you are customizing the dialog; the reporter carries a built-in copy of both files.

{% hint style="danger" %}
**Adding `BugSplatReporter.exe` is not optional, and forgetting it does not produce an error.** `BugSplatMonitor.exe` links the same uploader the reporter uses, so if the reporter isn't there it posts the report itself and your crashes keep arriving — without the dialog. Nobody is prompted for a description, no support response is shown, and the user is never given the chance to decline. The only signal is a line in the crash folder's `BugSplat.log`:

```
BugSplatReporter.exe not found next to BugSplatMonitor.exe - uploading in-process
without the crash dialog. Add BugSplatReporter.exe to your installer.
```

After upgrading, force a crash on a machine that installed from your installer — not from your build output — and confirm the dialog appears.
{% endhint %}

No code changes are required for the reporter split. The API is unchanged, and neither executable is named anywhere in your source.

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

