# Custom Engine Support

BugSplat supports crash reporting for engines and frameworks not listed in our official documentation.

### How it works

BugSplat's crash reporting is built on open standards. If your engine can produce a minidump, a [Breakpad](../cross-platform/breakpad.md)/[Crashpad](../cross-platform/crashpad/) report, a PLCrashReporter log (iOS/macOS), or expose a crash callback where you can invoke a handler, BugSplat can likely ingest and process your crashes. On Android and Linux, Crashpad is the supported path.

### Uploading crashes

The recommended upload path is a three-step process using presigned URLs:

1. **Request a presigned upload URL** - `GET https://{{database}}.bugsplat.com/api/getCrashUploadUrl`
2. **Upload your zipped crash file** - `PUT {{presigned_url}}` with `Content-Type: application/octet-stream`
3. **Commit the upload** - `POST https://{{database}}.bugsplat.com/api/commitS3CrashUpload`

Full details and a working Python example are available in the [Crash Post Endpoints documentation](https://docs.bugsplat.com/introduction/development/web-services/crash).

When committing the upload in step 3, you'll specify a `crashType` that tells BugSplat how to process the report. Common values for custom engine scenarios:

| Format                                        | crashType         |
| --------------------------------------------- | ----------------- |
| Windows minidump                              | `Windows.Native`  |
| Breakpad / Crashpad (Windows, Linux, Android) | `Crashpad`        |
| iOS / macOS PLCrashReporter                   | `PLCrashReporter` |
| Custom XML report                             | `XmlReport`       |

For platforms that cannot upload via presigned URLs, BugSplat also supports direct POST endpoints for Crashpad, XML, PS4, PS5, and others. See the [Crash Post Endpoints documentation](https://docs.bugsplat.com/introduction/development/web-services/crash) for the full list.

#### XML as a fallback

If your engine doesn't produce a standard crash format, the XML endpoint (`POST https://{{database}}.bugsplat.com/post/xml/index.php`) accepts a custom-structured report. You provide the stack frames, module list, and exception data in BugSplat's XML schema. This is the most flexible path for fully custom engines, and it also works well for scripting languages (Lua, Python, etc.) embedded in your engine where a native crash handler isn't practical.

### Symbol upload

BugSplat's symbol upload API accepts standard symbol formats. As long as you can produce symbols during your build process, stack traces can be symbolicated regardless of engine.

### What we need from you

The right integration path depends on your engine's crash handling architecture, platform targets, and build pipeline. Before reaching out, it helps to know:

* What platforms you're targeting (Windows, Linux, macOS, iOS, console)
* How your engine handles crashes today (signal handler, SEH, custom callback, none)
* Whether your engine produces minidumps, Breakpad reports, PLCrashReporter logs, or raw crash data
* Whether you have a symbol generation step in your build

### Get in touch

If you're building on a custom or proprietary engine and want to know whether BugSplat will work for you, email [joey@bugsplat.com](mailto:joey@bugsplat.com) with a brief description of your setup. We'll give you a straight answer.
