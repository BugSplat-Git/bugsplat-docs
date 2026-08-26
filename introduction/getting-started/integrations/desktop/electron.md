# Electron

## Overview 👀

{% hint style="info" %}
Want to see a sample Electron application integrated with BugSplat? Check out the [example app](https://github.com/BugSplat-Git/bugsplat-electron/tree/main/example) that ships with `@bugsplat/electron`!
{% endhint %}

BugSplat's [`@bugsplat/electron`](https://github.com/BugSplat-Git/bugsplat-electron) package collects everything an Electron app can fail with, from a single `init()` call in the main process:

* **Native crashes** in every process (main, renderers, GPU, utility processes and native addons) as [Crashpad](https://github.com/chromium/crashpad) minidumps. BugSplat resolves Electron's own symbol files automatically.
* **JavaScript errors** in the main process and in every renderer. Renderer reports travel to the main process over IPC, so no Content Security Policy changes are required.
* **Renderer hangs**, with the JavaScript call stack that was running when the page stopped responding.
* **Out-of-memory crashes**, with the JavaScript stack, heap statistics and OOM location as searchable attributes (Electron 42.10.1 and later).
* **One identity everywhere**: the user, email, application key, description and attributes you set in main or in a renderer are applied to native crash keys in every process and to every JavaScript report.

`@bugsplat/electron` supports Electron 35 and later. It replaces the previous two-step integration of `electron.crashReporter` plus [bugsplat-node](../cross-platform/node.js.md); see [Upgrading](#upgrading-from-crashreporter-and-bugsplat-node) below if you use that today.

## Installation 🏗

Install the package:

```bash
npm i @bugsplat/electron
```

Add a `database` property with the value of your BugSplat database to `package.json`. Be sure to replace `your-bugsplat-database` with the actual value of your BugSplat database:

```json
{
    ...
    "database": "your-bugsplat-database"
}
```

## Configuration ⚙️

### Main process

Call `init` at the top level of your main script, before `app.whenReady()`:

```typescript
import { init } from '@bugsplat/electron';

const { database, name, version } = require('./package.json');

const bugsplat = init({ database, application: name, version });

bugsplat.setUser('fred@bedrock.com');
bugsplat.setAttribute('plan', 'pro');
```

`init` starts Electron's `crashReporter` with BugSplat's submit URL, hooks `uncaughtException` and `unhandledRejection`, registers BugSplat's preload script in every session, and listens for renderer hangs and process exits. It never throws, and it is safe to call once.

### Renderer

Add one line to your renderer code:

```typescript
import { init } from '@bugsplat/electron/renderer';

init();
```

If your renderer has no bundler, load the global build instead and call `BugSplatElectron.init()`:

```html
<script src="./vendor/renderer.global.js"></script>
<script>
    BugSplatElectron.init();
</script>
```

Copy `node_modules/@bugsplat/electron/dist/renderer.global.js` next to your page at build time.

### Options

Everything except `database` is optional. The most commonly used options are:

```typescript
init({
    database: 'fred',
    application: 'my-electron-app',      // defaults to app.name
    version: '1.2.3',                    // defaults to app.getVersion()
    appKey: 'en-US',                     // sent as `key` on native reports
    user: 'Fred',
    email: 'fred@bedrock.com',
    description: 'Description!',         // sent as `comments` on native reports
    attributes: { plan: 'pro' },         // searchable attributes on every report
    additionalFilePaths: ['app.log'],    // attached to main-process JS reports
    main: { exitOnUncaughtException: true },
    transport: 'net',                    // send JS reports through Electron's proxy-aware net.fetch
});
```

The [README](https://github.com/BugSplat-Git/bugsplat-electron#configuration) documents every option, including `native` (the `crashReporter.start` options), `preload`, `processGone`, `unresponsive` and `beforePost`.

The client returned by `init` exposes the same API in the main process and in renderers:

```typescript
bugsplat.setAppKey(appKey);           // Additional metadata that can be queried via BugSplat's web application
bugsplat.setUser(user);               // The name or id of your user
bugsplat.setEmail(email);             // The email of your user
bugsplat.setDescription(description); // A description that can be overridden at post time
bugsplat.setAttribute(key, value);    // Searchable key/value attributes
bugsplat.post(error, options);        // Posts an arbitrary Error object; resolves { error, response, original }
bugsplat.postFeedback(title, options); // Posts user feedback
```

## Generate a Crash 💥

Throw an error in the main process or in a renderer:

```typescript
throw new Error('BugSplat!');
```

Generate a native crash in any process:

```typescript
process.crash();
```

## View BugSplat Crash Report

Navigate to the [Crashes](https://app.bugsplat.com/v2/crashes) page in BugSplat. You should see new crash reports for your application. Click the link in the **ID** column to see details about your crash on the [Crash](https://app.bugsplat.com/v2/crash?id=1) page:

<figure><img src="../../../../.gitbook/assets/image (34).png" alt=""><figcaption><p>Electron Native Crashes</p></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (37).png" alt=""><figcaption><p>TypeScript Error in Main Process</p></figcaption></figure>

## How it works 🔬

| Failure                                                                                            | Captured by                                                                | Arrives at BugSplat as                                          |
| -------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------- | --------------------------------------------------------------- |
| Native crash (main, renderer, GPU, utility, `.node` addon)                                         | Electron `crashReporter` (Crashpad)                                        | Minidump with native call stacks and your crash keys            |
| JavaScript error in main                                                                           | `uncaughtException` / `unhandledRejection`                                 | JavaScript report                                               |
| JavaScript error in a renderer                                                                     | `window` `error` / `unhandledrejection` → preload bridge → IPC → main      | JavaScript report with the renderer call stack, URL and title   |
| Renderer hang                                                                                      | `unresponsive` + `WebFrameMain.collectJavaScriptCallStack()`               | `RendererUnresponsive` report grouped by the hung JS frame      |
| Renderer out of memory                                                                             | Electron ≥ 42 writes `electron.v8-oom.*` crash keys                        | Minidump with `electron.v8-oom.stack` and heap statistics       |
| Renderer/child process exit without a minidump (`abnormal-exit`, `launch-failed`, `integrity-failure`) | `render-process-gone` / `child-process-gone`                          | `RenderProcessGone` / `ChildProcessGone` report                 |

A native crash report contains native frames from the minidump; JavaScript frames come from the hang and OOM mechanisms above or from JavaScript reports. Electron does not provide a single interleaved native + JavaScript stack, so a crash inside a native addon shows the native stack only.

## Renderer setup 🖥️

* Electron's default sandbox and context isolation are fully supported. BugSplat's preload is registered automatically with `session.registerPreloadScript`; you don't need to change your own preload.
* No `connect-src` entry in your Content Security Policy is required: renderer reports are posted by the main process.
* **Hang call stacks** require the page to opt in with the `Document-Policy: include-js-call-stacks-in-crash-reports` response header. Pages loaded with `loadFile` (`file://`) can't set headers, so serve your UI from a custom scheme and add the header with the `withJsCallStackPolicy` helper:

```typescript
import { withJsCallStackPolicy } from '@bugsplat/electron';

protocol.handle('app', async (request) => {
    const response = await net.fetch(pathToFileURL(fileFor(request.url)).toString());
    return new Response(response.body, {
        headers: withJsCallStackPolicy({ 'content-type': 'text/html' }),
    });
});
```

* If your renderer is built with React or Angular, keep using [@bugsplat/react](../web/react.md) or [bugsplat-ng](../web/angular.md) for component-tree errors. The renderer client is a `BugSplat` instance, so `@bugsplat/react`'s `ErrorBoundary` can reuse it via `scope={{ getClient }}`.

## Symbols and source maps 🗺️

BugSplat automatically resolves Electron and Electron Framework symbol files. Upload symbols for the parts of your application that you build yourself with [@bugsplat/symbol-upload](../../../development/working-with-symbol-files/upload-symbols-with-symbol-upload.md), for every released version.

Source maps let BugSplat map minified JavaScript function names, file names and line numbers back to their original values:

```json
{
    "scripts": {
        "upload-source-maps": "npx @bugsplat/symbol-upload -i $SYMBOL_UPLOAD_CLIENT_ID -s $SYMBOL_UPLOAD_CLIENT_SECRET -d ./dist -f \"**/*.js.map\""
    }
}
```

If your application uses a [node native addon](https://nodejs.org/api/addons.html), generate and upload Breakpad symbols from each binary with the `-m` argument:

```bash
npx @bugsplat/symbol-upload -i $SYMBOL_UPLOAD_CLIENT_ID -s $SYMBOL_UPLOAD_CLIENT_SECRET -d ./dist -f "**/*.node" -m
```

Verify that your symbols show up on the [Versions](https://app.bugsplat.com/v2/versions) page. Create client credentials on the [Integrations](https://app.bugsplat.com/v2/settings/database/integrations) page.

## Upgrading from crashReporter and bugsplat-node 📢

If your app calls `crashReporter.start` and creates `BugSplatNode` clients itself, replace both with `init`:

* Remove your `crashReporter.start({ submitURL: 'https://<database>.bugsplat.com/post/electron/v2/crash.php', globalExtra: { product, version, key, email, comments } })` call: `init` starts the crash reporter with the same endpoint and sends `product`/`version` for you. Move `key`, `email` and `comments` to the `appKey`, `email` and `description` options so they can change at runtime.
* Remove `bugsplat-node` and your `uncaughtException`/`unhandledRejection` handlers in main; `init` installs them. Set `main: { exitOnUncaughtException: true }` if you quit after an error today.
* Remove the `bugsplat` client and `window.onerror` handler from each renderer and call `init()` from `@bugsplat/electron/renderer` instead; you can also drop the `connect-src https://<database>.bugsplat.com` CSP entry.

## Contributing 🤝

BugSplat loves open-source software! If you have suggestions on how we can improve this integration, please reach out to support@bugsplat.com, create an [issue](https://github.com/BugSplat-Git/bugsplat-electron/issues) in our [GitHub repo](https://github.com/BugSplat-Git/bugsplat-electron) or send us a [pull request](https://github.com/BugSplat-Git/bugsplat-electron/pulls).
