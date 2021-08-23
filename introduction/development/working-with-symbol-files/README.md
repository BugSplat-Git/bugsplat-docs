# Symbol Files

Symbol files are a critical component of getting useful data from your crashes. Windows, Breakpad, Crashpad, Mac, and some JavaScript applications require [symbol files](https://www.bugsplat.com/resources/development/guide-to-symbol-files/) to be uploaded to generate call stacks containing function names and line numbers. The symbol upload process can be automated as part of your build. Symbols are required by many of our SDKs and can be uploaded and managed via the [Symbols](https://app.bugsplat.com/v2/symbols) page. 

For additional information regarding Symbol files, please visit the [documentation](https://www.bugsplat.com/docs/sdk/) specific to your application's platform

### What are Symbols?

Symbols are files that contain information to map information in the minidump file to file names and line numbers in source code.

### Using Symbols

For Windows [C++](https://www.bugsplat.com/docs/sdk/cplusplus) and [.NET](https://www.bugsplat.com/docs/sdk/dot-net) applications, symbols are files with **.exe**, **.pdb** and **.dll** extensions and can be uploaded automatically via [SendPdbs](https://www.bugsplat.com/docs/faq/sendpdbs/).

For [Crashpad](https://www.bugsplat.com/docs/sdk/crashpad) applications, symbols are files with **.sym** extensions and can be uploaded automatically via [symupload](https://github.com/google/breakpad/blob/main/docs/processor_design.md#processing).

For [macOS](https://www.bugsplat.com/docs/sdk/os-x) applications, symbols are files with **.app** and **.dSYM** extensions and can be uploaded automatically in a post-archive build action.

For TypeScript and [JavaScript](../../getting-started/integrations/web/javascript.md) applications, symbols are files with a **.js.map** extension and can be uploaded automatically via [@bugsplat/symbol-upload](https://www.npmjs.com/package/@bugsplat/symbol-upload).

![](../../../.gitbook/assets/screen-shot-2021-07-14-at-4.39.12-pm.png)



## Additional Resources

{% page-ref page="source-maps.md" %}

{% page-ref page="how-to-manually-upload-symbols.md" %}

{% page-ref page="working-with-windows-symbols-in-bugsplat.md" %}



