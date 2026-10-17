# Unreal GPU Crash Reporting

This guide explains how BugSplat reports GPU crashes from Unreal Engine games on Windows, which engine settings give you the most useful reports, and how to test the setup.

## Overview

A GPU crash happens when the GPU hangs, faults on a bad memory address, or its driver fails. Windows resets the GPU (a TDR, timeout detection and recovery), and the next Direct3D 12 or Vulkan call on the CPU fails with `DXGI_ERROR_DEVICE_REMOVED` or `VK_ERROR_DEVICE_LOST`. Unreal catches that error, logs what the GPU was doing, and sends a crash report with the crash type `GPUCrash`. The player sees "GPU Crashed or D3D Device Removed".

The CPU call stack in a GPU crash report shows where Unreal noticed the failure, not what caused it. In Unreal 5.8 these reports group under `ReportGPUCrash`. The cause is in data Unreal collects from the GPU side, and BugSplat shows that data on the crash's **GPU** tab:

| Section | What it shows | Where it comes from |
| --- | --- | --- |
| Fault | Hang, page fault or device removed, plus the `DXGI_ERROR_*` reason | Crash context and log |
| Hardware | GPU, vendor, driver version, VRAM, CPU, RHI | Crash context (`EngineData.RHI.*`) |
| Breadcrumbs | The render passes each GPU queue was running, with the active ones highlighted | RHI breadcrumbs in the crash context and log |
| Page fault | The faulting GPU address and the resources at that address, live or recently freed | DRED |
| Aftermath | Whether an NVIDIA Aftermath `.nv-gpudmp` dump was expected and attached | Aftermath |

On a crash group, the GPU panel splits GPU crashes into sub-groups by fault type, vendor and active render pass, and lists the GPUs, drivers and CPUs they come from. A spike on one driver version or one CPU family stands out there. Unstable 13th and 14th generation Intel CPUs, for example, produce crashes that look like GPU errors.

GPU crash reports use Unreal's normal crash reporting path, so if you already send crashes to BugSplat with the [Unreal Engine Plugin](unreal-engine-plugin.md), you are already receiving them.

## What Unreal Collects by Default

| Data | Debug / Development | Test / Shipping | Cost |
| --- | --- | --- | --- |
| RHI breadcrumbs (`r.GPUCrashDebugging.Breadcrumbs`) | On | Off | Small but measurable GPU cost |
| Lightweight DRED markers (`r.D3D12.LightweightDRED`) | On | Off | Small but measurable GPU cost |
| Full DRED (`-dred`) | Off | Off | Significant; don't ship it |
| NVIDIA Aftermath crash dumps | On for supported NVIDIA GPUs | On for supported NVIDIA GPUs | Epic reports no cost for the base dump |
| GPU validation (`-gpucrashdebugging`, `-d3ddebug`) | Off | Off | Very high; debugging only |

Without breadcrumbs, a Shipping report from an AMD or Intel GPU has little more than the device-removed reason. We recommend turning breadcrumbs on in every build.

## Recommended Configuration

Add this to your project's `Config/DefaultEngine.ini`:

```ini
[ConsoleVariables]
; Record which render pass each GPU queue was running, in every build configuration.
r.GPUCrashDebugging.Breadcrumbs=1
```

Fortnite ships with RHI breadcrumbs on. Profile your own title before you ship the change, especially on low-end GPUs.

Breadcrumbs reach BugSplat in two places: the crash context, which Shipping builds keep, and the game log. Some details, such as DRED page-fault resources and Aftermath status lines, appear only in the log. Shipping builds write no log unless your `*.Target.cs` sets `bUseLoggingInShipping = true`.

If you need the faulting resource of GPU page faults from players, you can also turn on lightweight DRED with `r.D3D12.LightweightDRED=1`. It costs more GPU time than breadcrumbs, so many studios turn it on only in QA builds or for players who have already hit a GPU crash.

### NVIDIA Aftermath Dumps

On supported NVIDIA GPUs, Unreal writes an Aftermath GPU crash dump (`.nv-gpudmp`), and sometimes shader debug information (`.nvdbg`), and includes them in the crash report. BugSplat lists them on the crash's **Attachments** tab as GPU dumps. Unreal logs that it writes the dump to `Saved/Logs`, then moves it into the crash report folder before the report is sent. Download the dump and open it in [NVIDIA Nsight Graphics](https://developer.nvidia.com/nsight-graphics) to see the faulting shader and the GPU state.

Aftermath doesn't always produce a dump. In cooked builds it can time out, which Unreal logs as `Timed out while waiting for Aftermath to start the GPU crash dump`. The GPU tab says when a dump was expected from an NVIDIA GPU but none arrived. That is useful information in itself: Epic treats a missing dump as a hint that the GPU itself did not report a fault.

To map Aftermath dumps to your shader source in Nsight Graphics, keep shader symbols from your build with `r.Shaders.Symbols=1`. Unreal writes them to `Saved/ShaderSymbols`. Keep them with your build's other symbols; don't ship them.

## Testing GPU Crash Reports

{% hint style="warning" %}
A test GPU crash really resets your GPU, and the screen goes black for a few seconds. Leave at least a minute between tests: five GPU resets within 60 seconds make Windows bugcheck (blue screen).
{% endhint %}

The BugSplat plugin's `GenerateGpuCrash` Blueprint function, also callable from C++, schedules a GPU crash on the next rendered frame:

```cpp
#include "BugSplatUtils.h"

UBugSplatUtils::GenerateGpuCrash(EBugSplatGpuCrashType::Hang);       // a shader that never finishes
UBugSplatUtils::GenerateGpuCrash(EBugSplatGpuCrashType::PageFault);  // a GPU access to unmapped memory
```

It runs Unreal's `GPUDebugCrash` console command, which you can also type into the console (`GPUDebugCrash hang` or `GPUDebugCrash pagefault`) or pass on the command line:

```
MyGame.exe -ExecCmds="GPUDebugCrash hang"
```

`GPUDebugCrash` exists in Debug, Development and Test builds. To test GPU crashes in a Shipping build, add `GlobalDefinitions.Add("ALLOW_GPUDEBUGCRASH_IN_SHIPPING=1");` to your game's `*.Target.cs`. Changing global definitions needs a unique build environment, which means a source build of the engine.

The [my-unreal-crasher](https://github.com/BugSplat-Git/my-unreal-crasher) sample has buttons for both GPU crash types and turns on RHI breadcrumbs in every build.

## Troubleshooting

* **No GPU tab on a GPU crash.** Check the crash's attributes for `RuntimeProperties.CrashType` = `GPUCrash` and the log attachment. Crashes that end in a GPU timeout without a device-removed error can exit without a report in some engine versions.
* **The GPU tab has no breadcrumbs.** Breadcrumbs are off in Test and Shipping by default. See [Recommended Configuration](#recommended-configuration).
* **`DXGI_ERROR_DEVICE_RESET`.** Another application caused the GPU reset; your game was a bystander.
* **Every GPU crash lands in the same crash group.** That's expected: the CPU call stack of a GPU crash is always Unreal's error handler (`ReportGPUCrash`). Use the GPU panel's sub-groups on that crash group to tell the causes apart.
* **A page fault's breadcrumbs point somewhere unexpected.** Breadcrumbs show which passes were still running when the fault was reported. A hang leaves the hung pass active, but a page fault is often reported while later passes are already running, so treat its active breadcrumbs as an area to look in, not an exact location. The page-fault address and the Aftermath dump are more precise.
