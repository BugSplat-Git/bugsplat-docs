# Symbol Files

Symbol files are critical to getting useful data from your crashes. [Windows](../../getting-started/integrations/desktop/cplusplus/), [Breakpad](../../getting-started/integrations/cross-platform/breakpad.md), [Crashpad](../../getting-started/integrations/cross-platform/crashpad/), [MacOS](../../getting-started/integrations/desktop/macos.md), and some [JavaScript](../../getting-started/integrations/web/javascript.md) applications require symbol files to be uploaded to generate call stacks containing function names and line numbers. The symbol upload process can be automated as part of your build. Many of our SDKs require symbols and can be uploaded and managed via the [Versions](https://app.bugsplat.com/v2/versions) page.

For additional information regarding Symbol files, please visit the [documentation](../../../) specific to your application's platform

### What are Symbols?

Symbols are files containing information to map the minidump file contents to file names and line numbers in source code.

### Using Symbols

[Windows C++](../../getting-started/integrations/desktop/cplusplus/) and [.NET](../../getting-started/integrations/desktop/windows-dot-net-framework.md) symbol files have **.exe**, **.pdb**, and **.dll** extensions.

For [Crashpad](../../getting-started/integrations/cross-platform/crashpad/) applications, symbols files contain **.sym** extensions

For [macOS](../../getting-started/integrations/desktop/macos.md) applications, symbols files end with a **.dSYM** extension.

For TypeScript and [JavaScript](../../getting-started/integrations/web/javascript.md) applications, symbols are files with a **.js.map** extension.

Symbol files can be uploaded via [symbol-upload](../../../education/faq/how-to-upload-symbol-files-with-symbol-upload.md). Additionally, Crashpad **.sym** files can be [generated automatically](../../../education/faq/how-to-upload-symbol-files-with-symbol-upload.md#improving-upload-speeds-1) by invoking symbol-upload with the `-m` argument.

![](<../../../.gitbook/assets/Screen Shot 2021-07-14 at 4.39.12 PM.png>)

## Additional Resources

{% content-ref url="source-maps.md" %}
[source-maps.md](source-maps.md)
{% endcontent-ref %}

{% content-ref url="how-to-manually-upload-symbols.md" %}
[how-to-manually-upload-symbols.md](how-to-manually-upload-symbols.md)
{% endcontent-ref %}

{% content-ref url="working-with-windows-symbols-in-bugsplat.md" %}
[working-with-windows-symbols-in-bugsplat.md](working-with-windows-symbols-in-bugsplat.md)
{% endcontent-ref %}
