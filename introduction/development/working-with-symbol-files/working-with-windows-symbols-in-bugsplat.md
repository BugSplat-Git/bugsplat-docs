# Working with Symbol Files in Windows Environments

A common stumbling block for new BugSplat users is properly uploading symbols for Windows \(native and .NET\). [Symbols](../../../education/bugsplat-terminology.md#symbols) are files that contain information to map the data in a crash file to file names and line numbers in source code.

To help make this issue easier we’ve put in a way to check if symbols have been uploaded properly.

## How it works: <a id="how-it-works"></a>

At the top of the Crash page you will see a warning if you're missing symbols.

![Missing Symbols Alert](../../../.gitbook/assets/windows-symbols-missing-symbols.png)

Once the data is obtained the fields of Function, Location, Code, OS, and Explanation will be populated with all available information. If a crash is missing symbols, there will be various messages under the 'File' column in the 'Active Thread' tab indicating which symbols were missing. Make sure to upload the missing symbols \(as indicated by the 'Active Thread' tab\) to the symbol store for your application name and version via the [Symbols](https://app.bugsplat.com/v2/symbols) page or [SendPdbs](../../../education/faq/using-sendpdbs-to-automatically-upload-symbol-files.md).

![Missing Symbols in Active Thread](../../../.gitbook/assets/windows-symbols-active-thread.png)

Additionally, you can click the 'Modules' tab to view information about all modules that were loaded on the system at crash time. In the modules table you’ll be able to view if symbols have been properly uploaded for each module by looking at the value under the 'Status' column.

![Missing Symbols in Modules](../../../.gitbook/assets/windows-symbols-modules-table.png)

The values under the status column are as follows:

* **deferred**: the debugger doesn’t need the module’s exe, dll, or pdb files to unwind the stack.
* **file not found**: the debugger needs the module’s exe, dll, or pdb files to unwind the stack, but the exe/dll with matching debug signature cannot be located.
* **symbols not found**: the debugger needs the module’s exe, dll, or pdb files to unwind the stack and the matching exe/dll has been located, but not the symbols.
* **pdb symbols**: debugger needs the module’s exe, dll, or pdb files to unwind the stack and they have been loaded successfully.

Unfortunately, Microsoft doesn’t provide symbol information for all of the operating system modules, so “file not found” is a fairly common occurrence.

Presence of a call stack warning that reads, “Stack unwind information not available. Following frames may be wrong.” is an indication that the debugger did not have access to the files it needed.

