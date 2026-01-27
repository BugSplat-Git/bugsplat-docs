# Unreal Assert, Check, and Ensure Reporting

This guide explains how to configure Unreal Engine's assertion system to send crash reports to BugSplat for `check`, `verify`, and `ensure` failures.

## Overview

Unreal Engine has three types of assertions:

| Type                                | Terminates App | Use Case                                             |
| ----------------------------------- | -------------- | ---------------------------------------------------- |
| `check` / `checkf` / `verify`       | Yes            | Fatal errors that should never happen                |
| `ensure` / `ensureMsgf`             | No             | Non-fatal errors worth investigating                 |
| `ensureAlways` / `ensureAlwaysMsgf` | No             | Non-fatal errors that should report every occurrence |

**Checks** always send crash reports when they fire (since they terminate the application). **Ensures** require additional configuration to send reports, which is the focus of this guide.

## How Ensure Reporting Works

There are two layers that control ensure crash reporting: **compile-time** and **runtime**.

Compile-time: In Debug and Development builds, ensures are always compiled in (`DO_ENSURE=1`). In Test and Shipping builds, ensures are compiled out by default—they just evaluate the expression and return the result with no logging or reporting. To keep ensures active in Shipping, set `bUseChecksInShipping = true` in your `*.Target.cs` file (this also enables `USE_ENSURES_IN_SHIPPING` since it follows the check setting by default).

Runtime: Once ensures are compiled in, the CVar `core.EnsuresAreErrors` (default `1`) controls whether they actually send crash reports. When set to `1`, ensure failures log as errors and submit reports; when set to `0`, they log as warnings only and skip reporting entirely. You can set this via INI (`[SystemSettings]` section) or console command. There's also `core.EnsureAlwaysEnabled` (default `true`) which controls whether `ensureAlways` fires every time or just once per callsite like regular `ensure`. Finally, the `-nocrashreports` command-line flag disables all report submission regardless of other settings.

In short: For ensures to send crash reports to BugSplat, you need `DO_ENSURE=1` (automatic in Dev builds, requires `bUseChecksInShipping` in Shipping) AND `core.EnsuresAreErrors=1` (the default) AND no `-nocrashreports` flag.

## Configuration by Build Type

### Development Builds (Default - No Changes Needed)

Ensures are compiled in and report by default. Just ensure your `DefaultEngine.ini` has BugSplat configured:

```ini
[CrashReportClient]
CrashReportClientVersion=1.0
DataRouterUrl="https://{database}.bugsplat.com/post/ue4/{appName}/{appVersion}"
```

### Shipping Builds (Requires Target.cs Change)

To receive ensure reports from Shipping builds, modify your game's `*.Target.cs`:

```csharp
public class MyGameTarget : TargetRules
{
    public MyGameTarget(TargetInfo Target) : base(Target)
    {
        Type = TargetType.Game;
        DefaultBuildSettings = BuildSettingsVersion.Latest;

        // Enable checks and ensures in Shipping builds
        bUseChecksInShipping = true;
    }
}
```

This sets `USE_CHECKS_IN_SHIPPING=1` and `USE_ENSURES_IN_SHIPPING=1`, keeping assertion macros active in packaged builds.

## INI Configuration Reference

### DefaultEngine.ini

```ini
[CrashReportClient]
; BugSplat endpoint (required)
DataRouterUrl="https://{database}.bugsplat.com/post/ue4/{appName}/{appVersion}"
CrashReportClientVersion=1.0

; Auto-send crashes without showing UI (recommended for packaged games)
bImplicitSend=true
bAgreeToCrashUpload=true

; Include log file in crash reports
bSendLogFile=true

; Whether to create minidumps for ensures (default: true)
Ensure.RecordDump=true

[Core.System]
; Percentage of ensures to handle (0-100). Use to reduce volume in production.
; Default: 100 (handle all ensures)
HandleEnsurePercent=100

[SystemSettings]
; Whether ensures send crash reports (1) or just log warnings (0)
; Default: 1
core.EnsuresAreErrors=1

; Whether ensureAlways fires every time (true) or once per callsite (false)
; Default: true
core.EnsureAlwaysEnabled=true

[HTTP]
; Increase timeouts for large crash uploads
HttpConnectionTimeout=90
HttpActivityTimeout=90
HttpTotalTimeout=90
```

### Reducing Ensure Volume in Production

If ensures are generating too many reports, you have several options:

Option: Throttle ensures by percentage

```ini
[Core.System]
HandleEnsurePercent=25
```

Option: Disable ensure reporting entirely (keeps logging)

```ini
[SystemSettings]
core.EnsuresAreErrors=0
```

Option: Convert ensureAlways to first-failure-only

```ini
[SystemSettings]
core.EnsureAlwaysEnabled=false
```

## Command-Line Options

| Flag                     | Effect                                                         |
| ------------------------ | -------------------------------------------------------------- |
| `-nocrashreports`        | Disables all crash report submission                           |
| `-handleensurepercent=N` | Override HandleEnsurePercent (e.g., `-handleensurepercent=50`) |
| `-CrashReporter`         | Include crash reporter when packaging (required for reports)   |

## Quick Reference: What Gets Reported?

| Build Config | Checks Report? | Ensures Report? | Notes                                |
| ------------ | -------------- | --------------- | ------------------------------------ |
| Debug        | Yes            | Yes             | All assertions active                |
| Development  | Yes            | Yes             | All assertions active                |
| Test         | No\*           | No\*            | \*Unless `bUseChecksInShipping=true` |
| Shipping     | No\*           | No\*            | \*Unless `bUseChecksInShipping=true` |

With `bUseChecksInShipping=true` in your Target.cs, Test and Shipping builds behave like Development for assertion reporting.

## Verifying Your Configuration

{% stepper %}
{% step %}
### Add a test ensure to your code

```cpp
ensure(false && "BugSplat test ensure");
```
{% endstep %}

{% step %}
### Run your packaged build
{% endstep %}

{% step %}
### Check your BugSplat dashboard for the incoming report
{% endstep %}

{% step %}
### Remove the test ensure once confirmed
{% endstep %}
{% endstepper %}

## Troubleshooting

<details>

<summary>Ensures not appearing in BugSplat</summary>

* Verify `bUseChecksInShipping=true` is set in your Target.cs (for Shipping builds)
* Check that `core.EnsuresAreErrors` is not set to `0`
* Confirm `-nocrashreports` is not on the command line
* Ensure `DataRouterUrl` is correctly configured

</details>

<details>

<summary>Too many ensure reports</summary>

* Use `HandleEnsurePercent` to throttle
* Set `core.EnsureAlwaysEnabled=false` to reduce `ensureAlways` spam
* Consider fixing the underlying issues causing ensures

</details>

<details>

<summary>Reports timing out</summary>

* Increase HTTP timeouts in `[HTTP]` section
* Check network connectivity to BugSplat servers

</details>
