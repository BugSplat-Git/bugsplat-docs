---
description: Testing Electron crashes with the sample application myElectronCrasher
---

# myElectronCrasher

The [my-electron-crasher](https://github.com/BugSplat-Git/my-electron-crasher) sample demonstrates how to use BugSplat's [npm package](https://www.npmjs.com/package/bugsplat) to track JavaScript errors in your Electron application. This sample also demonstrates how to use Electron's [Breakpad](https://github.com/google/breakpad/blob/master/docs/getting_started_with_breakpad.md) based [crashReporter](https://github.com/electron/electron/blob/master/docs/api/crash-reporter.md) for tracking native C++ crashes with BugSplat. Follow these steps to get started:

1. Clone this repository or download the zip from the releases tab
2. Open terminal or command prompt in this project's root directory
3. `npm install && npm run start`
4. Click any of the crash buttons in the application window to test the BugSplat integration
5. Navigate to BugSplat's [Crashes](https://app.bugsplat.com/v2/allcrash) page in your web browser
6. When prompted to log in, use the username "fred@bugsplat.com" and password "Flintstone"
7. Click the ID of your crash to see crash details

BugSplat will automatically resolve symbols for native Electron stack frames. If you'd like to try BugSplat with an Electron native plugin you've developed you will need to generate and upload [symbol files](https://github.com/google/breakpad/blob/master/docs/symbol_files.md). See Breakpad's [symupload](https://github.com/google/breakpad/blob/master/docs/getting_started_with_breakpad.md#build-process-specificssymbol-generation) utility to post symbols to BugSplat. More information about posting Breakpad symbols to BugSplat can be found [here](https://www.bugsplat.com/docs/sdk/breakpad).

For more information about getting started with Electron check out the [Quick Start Guide](http://electron.atom.io/docs/tutorial/quick-start) within the Electron documentation. For additional help using BugSplat, check out the [documentation](https://www.bugsplat.com/docs) on our website or email support\(at\)bugsplat.com if you have any questions.

