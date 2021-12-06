# Source Maps

BugSplat has the ability to convert uglified and minified JavaScript stack traces into function names and line numbers from the original source. You can upload [source map](https://developer.mozilla.org/en-US/docs/Tools/Debugger/How\_to/Use\_a\_source\_map) files to BugSplat during your build process by leveraging [@bugsplat/symbol-upload](https://www.npmjs.com/package/@bugsplat/symbol-upload).

In order to correctly unwind uglified stack traces, you’ll need to ensure that the filename of the source map matches the file name of the original source file. For instance, if your build process generates a file `main.153ee63b.chunk.js`, the corresponding source map file should be named `main.153ee63b.chunk.js.map`.&#x20;

{% hint style="info" %}
Be sure to upload source maps for each released version of your application for best results.
{% endhint %}

If BugSplat is unable to convert the uglified stack trace back to the original source the **Debugger Output** tab will display diagnostic information from the BugSplat backend that will help you troubleshoot.
