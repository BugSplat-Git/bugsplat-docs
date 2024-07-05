# Electron

## Overview 👀

{% hint style="info" %}
Want to see a sample Electron application integrated with BugSplat? Check out [my-electron-crasher](https://github.com/BugSplat-Git/my-electron-crasher)!
{% endhint %}

BugSplat supports the collection of both [electron.crashReporter](https://www.electronjs.org/docs/api/crash-reporter) (native) and [node.js](node.js.md) crash reports. Native crashes are generated via [Crashpad](https://github.com/chromium/crashpad) and BugSplat requires symbol files in order to calculate the call stack.

BugSplat will automatically resolve Electron framework symbol files when calculating call stacks. However, if your application includes native add-ons or is packaged with [electron-builder](https://github.com/electron-userland/electron-builder) you will need to upload application-specific symbol files to see full native call stacks. All symbol files must be uploaded to BugSplat via [@bugsplat/symbol-upload](../../../../education/faq/how-to-upload-symbol-files-with-symbol-upload.md), [symupload](https://github.com/google/breakpad/blob/master/docs/getting\_started\_with\_breakpad.md#build-process-specificssymbol-generation), or manually via the [Versions page](https://app.bugsplat.com/v2/versions). More information about uploading symbol files to BugSplat can be found [here](crashpad/how-to-build-google-crashpad.md#uploading-symbols).

BugSplat-node can also be used to collect [uncaughtException](https://nodejs.org/api/process.html#process\_event\_uncaughtexception) and [unhandledRejection](https://nodejs.org/api/process.html#process\_event\_unhandledrejection) events in your application's JavaScript code.

## Native (C++) Crash Reporting ⚙️&#x20;

Configure [electron.crashReporter](https://github.com/electron/electron/blob/master/docs/api/crash-reporter.md) to upload crash reports to BugSplat using the following steps. BugSplat will automatically download Electron and Electron Framework symbol files. If your application uses [Native Node Modules](https://www.electronjs.org/docs/latest/tutorial/using-native-node-modules), you will need to generate and upload symbol files to resolve call stacks correctly.

### Update Package.json

Add a `database` property with the value of your BugSplat database to `package.json`. Be sure to replace `your-bugsplat-database` with the actual value of your BugSplat database.

```json
{
    ...
    "database": "your-bugsplat-database"
}
```

### Configure Crash Reporter

Next, call `crashReporter.start` as shown in the example below. You may optionally pass globalExtra values for `key`, `email`, `comments`, `notes`, and `ipAddress`.

Note that the `globalExtra` fields will be sent with crashes captured on all processes:

```javascript
const { crashReporter } = require("electron");
const { database, name, version } = require("../../package.json");
crashReporter.start({
  submitURL: `https://${database}.bugsplat.com/post/electron/v2/crash.php`,
  ignoreSystemCrashHandler: true,
  uploadToServer: true,
  rateLimit: false,
  globalExtra: {
    "product": name,
    "version": version,
    "key": "en-US",
    "email": "fred@bugsplat.com",
    "comments": "BugSplat rocks!",
  }
})
```

For more information on configuring `electron.crashReporter` including adding properties to individual processes, please see the [Electron crashReporter documentation](https://www.electronjs.org/docs/latest/api/crash-reporter).

### Generate a Crash

Generate a crash in one of the Electron processes to test your BugSplat integration:

```typescript
process.crash()
```

### View BugSplat Crash Report

Navigate to the [Crashes](https://app.bugsplat.com/v2/crashes) page in BugSplat. You should see a new crash report for your application. Click the link in the **ID** column to see details about your crash on the [Crash](https://app.bugsplat.com/v2/crash?id=1) page:

<figure><img src="../../../../.gitbook/assets/image (34).png" alt=""><figcaption><p>Electron Native Crashes</p></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (35).png" alt=""><figcaption><p>Electron Native Crash</p></figcaption></figure>

### Optional: Upload Native Addon Symbols

If your application uses a [node native addon](https://nodejs.org/api/addons.html), you must generate and upload symbols for each binary and for every build.

Use [@bugsplat/symbol-upload](../../../../education/faq/how-to-upload-symbol-files-with-symbol-upload.md#improving-upload-speeds-1) and the `-m` argument to generate and upload `.sym` files from your application binaries automatically. You can run symbol-upload without any arguments to view all available options.

```bash
npx symbol-upload -u you@email.com -p password -d ./dist -f "**/*.node" -m
```

Alternatively, you can add a build step to generate and upload `.sym` files for your node native addon using standard Breakpad tools. To generate symbol files, you can run [dump\_syms](crashpad/how-to-build-google-crashpad.md#generating-symbols) with a path to a `.node` file after the Node Native Module build or rebuild step if you're using a tool like [electron-rebuild](https://github.com/electron/electron-rebuild). Once you've generated a `.sym` file for your `.node` native module, the `.sym` file can be uploaded via [symupload](crashpad/how-to-build-google-crashpad.md#uploading-symbols), or manually on the [Versions](https://app.bugsplat.com/v2/versions?database=Fred) page.

Verify that your node native addon `.sym` files show up on the [Versions](https://app.bugsplat.com/v2/versions) page. Be sure to upload symbols for each released version of your application. Integrate [@bugsplat/symbol-upload](../../../../education/faq/how-to-upload-symbol-files-with-symbol-upload.md) or [symupload](crashpad/how-to-build-google-crashpad.md#uploading-symbols) into your build and release processes for best results.

## JavaScript/TypeScript Error Reporting 💥

To configure reporting of JavaScript or TypeScript errors in your main and renderer processes, please see our [node.js](node.js.md) documentation for installing and configuring [bugsplat-node](https://github.com/BugSplat-Git/bugsplat-node).

{% content-ref url="node.js.md" %}
[node.js.md](node.js.md)
{% endcontent-ref %}

### Installing bugsplat-node

Install bugsplat-node via the following command:

```bash
npm i bugsplat-node
```

### Configuration

Next `import` or `require` BugSplat in both your main and renderer threads.

```typescript
import { BugSplatNode as BugSplat } from "bugsplat-node"
const bugsplat = new BugSplat(database, name, version)
```

In addition to the configuration demonstrated above, there are a few public methods that can be used to customize your BugSplat integration:

```typescript
bugsplat.setDefaultAppKey(appKey); // Additional metadata that can be queried via BugSplat's web application
bugsplat.setDefaultUser(user); // The name or id of your user
bugsplat.setDefaultEmail(email); // The email of your user 
bugsplat.setDefaultDescription(description); // A description placeholder that can be overridden at crash time
bugsplat.setDefaultAdditionalFilePaths([paths]); // Paths to files to be sent to BugSplat at post time (limit 1MB) 
bugsplat.postAndExit(error, options); // Wrapper for post that calls process.exit(1) after posting error to BugSplat
bugsplat.post(error, options); // Aysnc function that posts an arbitrary Error object to BugSplat
// If the values options.appKey, options.user, options.email, options.description, options.additionalFilePaths are set the corresponding default values will be overwritten
// Returns a promise that resolves with properties: error (if there was an error posting to BugSplat), response (the response from the BugSplat crash post API), and original (the error passed by bugsplat.post)
```

### Main Thread

Create an error handler for `uncaughtExceptions` and `unhandledPromise` rejections. We recommend you quit your application in the event of an `uncaughtException` or `unhandledPromiseRejection`. You may also want to add code to display a message to your user here:

```typescript
const javaScriptErrorHandler = async (error) => {
  await bugsplat.post(error);
  app.quit();
}
```

Add the error handler created above to `uncaughtException` and `unhandledPromiseRejection` events:

```typescript
process.on('unhandledRejection', javaScriptErrorHandler)
process.on('uncaughtException', javaScriptErrorHandler)
```

### Renderer Thread

Many Electron applications run multiple node.js processes. For instance, the [electron-quick-start](https://github.com/electron/electron-quick-start) application runs both a [main](https://github.com/electron/electron-quick-start/blob/master/main.js) and a [renderer](https://github.com/electron/electron-quick-start/blob/master/renderer.js) process. You must require `bugsplat` in each process you want to capture errors. To capture errors in the renderer process, add the following to renderer.js:

Reloading or quitting the application is sometimes desirable when an error occurs in the renderer process. The following is an example of how to invoke the main process from the renderer and quit your application in the case of an unhandled exception in the renderer:

[renderer.ts](https://github.com/BugSplat-Git/my-electron-crasher/blob/master/src/renderer.ts)

```typescript
window.onerror = async (messageOrEvent, source, lineno, colno, error) => {
await bugsplat.post(error)
  ipcRenderer.send('rendererCrash')
}
```

[main.ts](https://github.com/BugSplat-Git/my-electron-crasher/blob/master/src/main.ts)

```typescript
import { ipcMain } from "electron";
ipcMain.on('rendererCrash', function () {
  // Display an error and reload or quit the app here
})
```

Test BugSplat by throwing a new error in either the main or renderer process:

```typescript
throw new Error("BugSplat!");
```

### Uploading Source Maps

If you're using TypeScript or another language that compiles to JavaScript, BugSplat can map uglified and minified JavaScript function names, file names, and line numbers back to their original values via [source maps](../../../development/working-with-symbol-files/source-maps.md).

To upload source maps, add a symbol-upload script that uploads all your build's `.js.map` files:

```json
{
  ...
  "scripts": {
    "upload-source-maps": "npx symbol-upload -u you@email.com -p password -d ./dist -f \"**/*.js.map\""
  }
}
```

### Viewing Error Reports

Run your application and generate an error report. Navigate to the [Crashes](https://app.bugsplat.com/v2/crashes) page in BugSplat to view reports for the application you just configured. Click the link in the **ID** column to see details about your crash on the [Crash](https://app.bugsplat.com/v2/crash?id=1) page:

<figure><img src="../../../../.gitbook/assets/image (36).png" alt=""><figcaption><p>TypeScrpt Error on Crashes Page</p></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (37).png" alt=""><figcaption><p>TypeScript Error in Main Process</p></figcaption></figure>

That’s it! Your Electron application is now configured to upload crash reports to BugSplat.

## Contributing 🤝

BugSplat loves open-source software! If you have suggestions on how we can improve this integration, please reach out to support@bugsplat.com, create an [issue](https://github.com/BugSplat-Git/bugsplat-node/issues) in our [GitHub repo](https://github.com/BugSplat-Git/bugsplat-node) or send us a [pull request](https://github.com/BugSplat-Git/bugsplat-node/pulls).
