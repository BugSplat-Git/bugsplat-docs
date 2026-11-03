# Symbol Files

Symbol files are critical to getting useful data from your crashes. [Windows](../../integrations/desktop/cplusplus/), [Breakpad](../../integrations/desktop/breakpad.md), [Crashpad](../../integrations/desktop/crashpad/), [MacOS](../../integrations/desktop/macos.md), [Xbox](../../integrations/game-development/xbox.md), [PlayStation](../../integrations/game-development/playstation.md), [Nintendo Switch 2](../../integrations/game-development/nintendo.md), and some [JavaScript](../../integrations/web/javascript.md) applications require uploading symbol files to generate call stacks that contain function names and line numbers. The symbol upload process can be automated as part of your build. Many of our SDKs require symbols, which can be uploaded and managed via the [Versions](https://app.bugsplat.com/v2/versions) page.

For additional information regarding Symbol files, please visit the [documentation](../../integrations/integrations.md) specific to your application's platform.

### What are Symbols?

Symbols are files containing information to map the crash report's call stack to file names and line numbers in source code.

### Using Symbols

[Windows C++](../../integrations/desktop/cplusplus/) and [.NET](../../integrations/desktop/bugsplat-for-dot-net.md) symbol files have **.exe**, **.pdb**, and **.dll** extensions.

For [Crashpad](../../integrations/desktop/crashpad/) applications, symbols files contain **.sym** extensions.

For [macOS](../../integrations/desktop/macos.md) applications, symbols files end with a **.dSYM** extension.

For TypeScript and [JavaScript](../../integrations/web/javascript.md) applications, symbols are files with a **.js.map** extension.

Symbol files can be uploaded via [symbol-upload](upload-symbols-with-symbol-upload.md). Additionally, Crashpad **.sym** files can be [generated automatically](upload-symbols-with-symbol-upload.md#improving-upload-speeds-1) by invoking symbol-upload with the `-m` argument.

<figure><img src="../../.gitbook/assets/image (23).png" alt=""><figcaption><p>BugSplat Versions Page</p></figcaption></figure>

## Additional Resources

{% content-ref url="upload-symbols-with-symbol-upload.md" %}
[upload-symbols-with-symbol-upload.md](upload-symbols-with-symbol-upload.md)
{% endcontent-ref %}

{% content-ref url="how-to-manually-upload-symbols.md" %}
[how-to-manually-upload-symbols.md](how-to-manually-upload-symbols.md)
{% endcontent-ref %}

{% content-ref url="managing-symbol-storage.md" %}
[managing-symbol-storage.md](managing-symbol-storage.md)
{% endcontent-ref %}

{% content-ref url="source-maps.md" %}
[source-maps.md](source-maps.md)
{% endcontent-ref %}

{% content-ref url="working-with-windows-symbols-in-bugsplat.md" %}
[working-with-windows-symbols-in-bugsplat.md](working-with-windows-symbols-in-bugsplat.md)
{% endcontent-ref %}
