# Find missing crash data

### Native Windows Applications

Modify your compiler settings to create symbol files for your production build.

* Make sure you’ve uploaded all symbol and executable files from our symbol management page.
* The application name and version number supplied when files are uploaded must match the associated parameters in the `MiniDmpSender` initialization call within your application. Please find more information about the `MiniDmpSender` on our `myCrasher` example page.
* Include the current version of `dbghelp.dll`, `BugSplat.dll`, `BugSplatRc.dll`, and `bssndprt.exe` when you install your application.

### .NET Managed Applications

* Managed application crash reporting works just like native application crash reporting in that minidumps are created on the client machine and sent to BugSplat for analysis through Microsoft debugging tools.

