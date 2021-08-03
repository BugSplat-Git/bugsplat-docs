# Symbol Files

Symbol files are a critical component of getting useful data from your crashes. Windows, Breakpad, Crashpad and Mac applications require [symbol files](https://www.bugsplat.com/resources/development/guide-to-symbol-files/) to be uploaded to generate call stacks containing function names and line numbers. The symbol upload process can be automated as part of your build.

See your platform's [docs](https://www.bugsplat.com/docs/sdk/) for additional information. They are required by many of our SDKs and can be uploaded and managed via the [Symbols](https://app.bugsplat.com/v2/symbols) page.

### What are Symbols?

Symbols are files that contain information to map information in the minidump file to file names and line numbers in source code.

### Using Symbols

For Windows [C++](https://www.bugsplat.com/docs/sdk/cplusplus) and [.NET](https://www.bugsplat.com/docs/sdk/dot-net) applications, symbols are files with **.exe**, **.pdb** and **.dll** extensions and can be uploaded automatically via [SendPdbs](https://www.bugsplat.com/docs/faq/sendpdbs/).

For [Crashpad](https://www.bugsplat.com/docs/sdk/crashpad) applications, symbols are files with **.sym** extensions and can be uploaded automatically via symupload.

For [OS X](https://www.bugsplat.com/docs/sdk/os-x) applications, symbols are files with **.app** and **.dSYM** extensions and can be uploaded automatically in a post-archive build action.

![](../../../.gitbook/assets/screen-shot-2021-07-14-at-4.39.12-pm.png)



## More resources

### How to manually upload symbol files 

{% page-ref page="how-to-manually-upload-symbols.md" %}

### Working with symbol files in Windows environments

{% page-ref page="working-with-windows-symbols-in-bugsplat.md" %}



