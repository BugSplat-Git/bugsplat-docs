# GPU Crash Reporting (Direct3D 12)

{% hint style="info" %}
GPU crash reporting is in preview.
{% endhint %}

BugSplat for Windows can report Direct3D 12 GPU crashes: GPU hangs that Windows resets (TDRs), GPU page faults, and device removal. This page shows how to add GPU crash reporting to a C++ application with the header-only `BugSplatGpu.h`.

## Why GPU Crashes Need Extra Data

The GPU runs command lists asynchronously. When it hangs or faults, Windows resets it, and the next Direct3D 12 or DXGI call on any CPU thread returns `DXGI_ERROR_DEVICE_REMOVED`. The CPU call stack shows that call, often `Present`, rather than the draw or dispatch that failed.

`BugSplatGpu.h` collects what the GPU side knows when the device is lost and attaches it to the crash report as `bugsplat-gpu.json`. BugSplat shows it on the crash's **GPU** tab:

* the GPU, vendor, driver version and VRAM, plus the CPU and OS
* the device-removed reason, such as `DXGI_ERROR_DEVICE_HUNG`
* [DRED](https://learn.microsoft.com/en-us/windows/win32/direct3d12/use-dred) breadcrumbs: the command lists that didn't finish, and the operation each one was running
* DRED page-fault data: the faulting GPU address and the resources at it, live or recently freed
* any GPU crash dump files your application wrote, such as NVIDIA Nsight Aftermath `.nv-gpudmp` dumps

## Adding GPU Crash Reporting

`BugSplatGpu.h` ships in the SDK's `inc` folder. Link with `d3d12.lib` and `dxgi.lib`.

```cpp
#include "BugSplat.h"
#include "BugSplatGpu.h"

BugSplat bugSplat(L"{database}", L"{application}", L"{version}");

// 1. Before creating the device: turn on DRED breadcrumbs and page-fault tracking.
BugSplatGpu::EnableDred();

// 2. After creating the device: tell BugSplat which adapter it runs on.
BugSplatGpu::GpuCrashReporter gpu(bugSplat);
gpu.SetAdapter(adapter);        // the IDXGIAdapter1 passed to D3D12CreateDevice
gpu.SetDredEnabled(true);

// 3. Wherever a D3D12 or DXGI call can report a lost device:
HRESULT hr = swapChain->Present(1, 0);
if (BugSplatGpu::IsDeviceLost(hr))
{
    gpu.ReportDeviceLost(device, hr);
}
```

`ReportDeviceLost` writes and attaches `bugsplat-gpu.json`, then raises the device-removed reason as an exception (for example `0x887A0006` for `DXGI_ERROR_DEVICE_HUNG`). BugSplat's crash handler catches it and posts the report, so it does not return. GPU crashes appear in BugSplat with that exception code.

### NVIDIA Nsight Aftermath

If your application uses [NVIDIA Nsight Aftermath](https://developer.nvidia.com/nsight-aftermath), wait for Aftermath to finish writing its dump after the device is lost, then pass the files to `ReportDeviceLost`:

```cpp
gpu.SetAftermathEnabled(true);
// ... after the device is lost, poll GFSDK_Aftermath_GetCrashDumpStatus for a few seconds ...
gpu.ReportDeviceLost(device, hr, { dumpPath, shaderDebugInfoPath }, aftermathTimedOut);
```

Don't wait indefinitely: Aftermath can time out, and a report without a GPU dump is better than no report. The dumps appear on the crash's **Attachments** tab. Open them in [NVIDIA Nsight Graphics](https://developer.nvidia.com/nsight-graphics).

## Performance

| Feature | Cost | Ship it? |
| --- | --- | --- |
| `BugSplatGpu.h` without DRED | Runs only after the device is lost | Yes |
| DRED auto-breadcrumbs | 2-5% GPU time on a typical AAA engine (Microsoft) | Often only in QA builds, or in a diagnostics mode for players who hit GPU crashes |
| DRED page-fault tracking | Extra memory and CPU time when resources are created and destroyed | As above |
| Aftermath base crash dump | Small | Yes |
| Aftermath event markers and resource tracking | Considerable CPU overhead (NVIDIA) | In a diagnostics mode |
| Aftermath call-stack capture | Very high CPU overhead | No |

`EnableDred(breadcrumbs, pageFaults)` lets you turn each DRED feature on separately.

## Sample

The [MyGpuCrasher](https://github.com/BugSplat-Git/bugsplat-windows/tree/main/Samples/MyGpuCrasher) sample makes the GPU fail on purpose:

* `/Hang`: a compute shader that never finishes, which Windows resets after 2 seconds
* `/PageFault`: a dispatch against a buffer that was already released
* `/DeviceRemoved`: `ID3D12Device5::RemoveDevice`, without a GPU reset

Build `MyGpuCrasher.sln` with `/p:AftermathSdkDir=<folder>` to add `/Aftermath`.

{% hint style="warning" %}
`/Hang` and `/PageFault` really reset your GPU, and the screen goes black for a few seconds. Leave at least a minute between runs: five GPU resets within 60 seconds make Windows bugcheck (blue screen).
{% endhint %}

NVIDIA GPUs often report a GPU page fault as `DXGI_ERROR_DEVICE_HUNG` with no DRED page-fault record. The breadcrumbs still show the operation that failed.
