# Why are Crashes Missing Symbols, Function Names, and/or Line Numbers?

To get the most out of BugSplat, it's essential to configure symbol uploads for each released version of your application. For most platforms, you'll need to upload `.dll`, `.pdb`, `.exe`, `.elf`, `.so`, and/or `.sym` files to get function names and line numbers in reports. Symbol files are matched to crash reports by a GUID that needs to be an exact match. BugSplat can't calculate function names and line numbers if the symbol files are from a different build or have been modified by a build process such as code signing or anti-cheat.&#x20;

{% hint style="info" %}
Symbolication of executables that use anti-cheat typically requires additional configuration within BugSplat.&#x20;
{% endhint %}

### Missing Symbols

When the correct symbols aren't available, the method column will display a module name or a hexadecimal address. For reports that are missing symbols, the function names and line numbers will be missing. On some platforms, BugSplat can detect that symbols aren't available and will display the text **Missing Symbols** in the report call stacks.&#x20;

<figure><img src="../../.gitbook/assets/image (74).png" alt=""><figcaption></figcaption></figure>

### Debugger Output

BugSplat runs various debuggers on our backend to process crash reports. Each of these debuggers captures information about the symbols required to unwind function names and line numbers for crash reports. The debugger output shows the paths the debugger checked for symbols, including the processor's local disk, system-defined symbol servers, and user-defined symbol servers. The paths will contain the GUID of the symbol requested by the debugger.

<figure><img src="../../.gitbook/assets/image (78).png" alt=""><figcaption><p>Crash Page Tabs</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (77).png" alt=""><figcaption><p>Debugger Output</p></figcaption></figure>

For Windows, BugSplat needs the `.dll` and `.exe`  files in addition to the `.pdb` files. For Crashpad, BugSplat needs the `.sym` files and for macOS, BugSplat needs `.dSYM` files.

### Versions Page

The [Versions](https://app.bugsplat.com/v2/versions) page displays a list of versions associated with crash reports and symbol uploads. You can see the size of the symbol store associated with each version.

<figure><img src="../../.gitbook/assets/image (81).png" alt=""><figcaption></figcaption></figure>

### Symbols Page

Clicking the link in the **Application** column navigates to the [Symbols](https://app.bugsplat.com/v2/symbols) page. By default, the Symbols page starts with an application and version filter and displays only a subset of symbols uploaded to BugSplat.

<figure><img src="../../.gitbook/assets/image (80).png" alt=""><figcaption></figcaption></figure>

The BugSplat backend can access files across symbol stores. Associating symbols with an application and version provides a logical grouping and makes cleanup easier, but it is not required to resolve symbols when processing a crash.

You can search across symbol stores by clicking **View All Symbols**. Search by GUID to check if BugSplat has access to the correct symbols.&#x20;

<figure><img src="../../.gitbook/assets/image (84).png" alt=""><figcaption><p>Symbols Page No Results</p></figcaption></figure>

Searching the GUID `B60B580592334258BBB3B8CD5BA7B2BF2` yielded no results; therefore, BugSplat will be unable to resolve file names and line numbers. You will need to upload the correct symbols with [symbol-upload](how-to-upload-symbol-files-with-symbol-upload.md) or manually via the BugSplat [web application](../../introduction/development/working-with-symbol-files/how-to-manually-upload-symbols.md).

Once the correct symbols have been uploaded, you will need to [reprocess](../how-tos/reprocessing-crashes.md) or [batch reprocess](../how-tos/batch-reprocess-crashes.md) crashes that rely on these symbols.

### External Symbol Servers

{% hint style="danger" %}
BugSplat will cache missed lookups to external symbol servers for 6 hours. If you upload symbols after they were requested, those symbol lookups will be skipped until the miss-cache entry clears.
{% endhint %}

BugSplat's [symbol-upload](https://docs.bugsplat.com/education/faq/how-to-upload-symbol-files-with-symbol-upload) tool can be used to create a [SymSrv-compatible](https://learn.microsoft.com/en-us/windows/win32/debug/symbol-servers-and-symbol-stores) directory structure that can be [self-hosted](how-to-upload-symbol-files-with-symbol-upload.md#self-hosted-symbol-servers). BugSplat supports connecting to external symbols via HTTP (including Azure Blob Storage) or AWS S3. Our backend will access the external symbols and cache them locally for faster lookup.

### Tools

We offer a few tools that you can use to verify the GUIDs of symbols on your local system.

{% embed url="https://github.com/BugSplat-Git/pdb-guid" %}

{% embed url="https://github.com/BugSplat-Git/macho-uuid" %}

{% embed url="https://github.com/BugSplat-Git/elfy" %}

Additionally, several other tools can be used to get GUIDs from symbol files. For Windows, you can use [dumpbin](https://learn.microsoft.com/en-us/cpp/build/reference/dumpbin-reference?view=msvc-170) and [symchk](https://learn.microsoft.com/en-us/windows-hardware/drivers/debugger/symchk-command-line-options). On macOS, [dwarfdump](https://llvm.org/docs/CommandGuide/llvm-dwarfdump.html) will report the symbol's GUID. For Crashpad, the GUID can be found on the first line of each `.sym` file.
