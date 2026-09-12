---
description: >-
  Post crashes the client already understands (script exceptions, managed
  exceptions, engine asserts) as BugSplat XML reports or their JSON mirror.
---

# Structured Reports

A structured report is a crash your code already understands: a script exception, an unhandled managed exception, an engine assert, an AddressSanitizer failure. Instead of a memory dump it carries the stack the runtime knows, in BugSplat's `bsCrashReport.xml` schema (crash type 21) or a JSON mirror of it, and goes through the same store and upload as everything else. `BugSplatDotNet` uses it for unhandled managed exceptions; engine and scripting integrations use it for theirs.

### Build and post

```c
bugsplat_report* r = bugsplat_report_new(BUGSPLAT_REPORT_XML);            /* or BUGSPLAT_REPORT_JSON */
bugsplat_report_set_platform(r, "Lua", "Windows 11 10.0.26200 x64");      /* prefilled from the environment */
bugsplat_report_set_exception(r, "runtime error", "attempt to index a nil value");
bugsplat_report_add_module(r, "game.dll", "C:/app/game.dll", 0x7ff600000000, 0x20000, "1.0.0.0", "1.0");
int32_t t = bugsplat_report_add_thread(r, "main", /*is_crashing_thread=*/1);
bugsplat_report_add_frame(r, t, "Player:update", "scripts/player.lua", 42, "", 0);
bugsplat_report_add_frame(r, t, "Game:tick",     "scripts/game.lua",   118, "", 0);
bugsplat_report_add_attachment(r, "C:/app/logs/game.log");

bugsplat_upload_result out = BUGSPLAT_UPLOAD_RESULT_INIT;
bugsplat_result rc = bugsplat_report_post(r, &out);       /* blocks until uploaded, unless the policy is MANUAL */
/* out.crash_id, out.info_url */
bugsplat_upload_result_free(&out);
bugsplat_report_free(r);
```

C++: `bugsplat::Report rep; int t = rep.Thread("main", true); rep.Frame(t, "Player:update", "scripts/player.lua", 42); auto result = rep.Post();`

The **crashing thread** is the one added with `is_crashing_thread = 1` (or the first thread). Its first frame becomes the report's function, file and line on the crash page; its frames feed the crash signature (`function|file|line` per frame, hashed), so identical stacks group instantly.

The report's own attachments and the session's attachments (`bugsplat_add_attachment`) are both included. User, email, key, description, notes, environment and attributes come from the current values, as for a crash.

### The XML the server receives

```xml
<report>
    <platform>Lua</platform>
    <os>Windows 11 10.0.26200 x64</os>
    <process>
        <exception>
            <code>runtime error</code>
            <explanation>attempt to index a nil value</explanation>
            <func><![CDATA[Player:update]]></func>
            <file>scripts/player.lua</file>
            <line>42</line>
        </exception>
        <modules numloaded="1">
            <module><name>game.dll</name><order>1</order><address>00007ff600000000-00007ff600020000</address>
                    <path>C:/app/game.dll</path><symbolsloaded>deferred</symbolsloaded>
                    <fileversion>1.0.0.0</fileversion><productversion>1.0</productversion>
                    <checksum>00000000</checksum><timedatestamp/></module>
        </modules>
        <threads count="1">
            <thread id="main" current="yes" event="yes" framecount="2">
                <frame><symbol><![CDATA[Player:update]]></symbol><file>scripts/player.lua</file><line>42</line></frame>
                <frame><symbol><![CDATA[Game:tick]]></symbol><file>scripts/game.lua</file><line>118</line></frame>
            </thread>
        </threads>
    </process>
</report>
```

The JSON form (`bsCrashReport.json`) has the same fields (`schemaVersion`, `platform`, `os`, `exception{code, explanation, registers}`, `modules[]`, `threads[{id, crashing, frames[{function, file, line, module, address}]}]`). Server-side acceptance of the JSON file under type 21 is being added; use XML until it is announced.

### Managed exceptions in .NET

```csharp
using var bugsplat = new BugSplat("fred", "MyApp", "1.0.0");
bugsplat.HandleApplicationExceptions();       // AppDomain.UnhandledException, TaskScheduler.UnobservedTaskException
// or explicitly:
var result = bugsplat.Post(exception);         // result.CrashId, result.InfoUrl
```

Each managed frame becomes a report frame with the method, file and line; inner exceptions become extra threads named `inner-0`, `inner-1`, ... Native faults in the same process are still captured as dumps by the monitor.

### AddressSanitizer

```c
bugsplat_post_asan_report(asan_output_text, &out);    /* crash type 25 */
```

Stores the sanitizer output verbatim as `bsAsanReport.xml`; the server parses it. See also [Address Sanitizer Reports](../../posting-a-test-crash/myconsolecrasher-c-plus-plus/address-sanitizer-reports.md).

### Policies

`DIALOG` and `QUIET` both upload a structured report immediately, without a dialog (the report was produced by code, not by a crash the user saw). `MANUAL` leaves it pending for `bugsplat_send_report` / `bugsplat_post_pending_reports_async`.
