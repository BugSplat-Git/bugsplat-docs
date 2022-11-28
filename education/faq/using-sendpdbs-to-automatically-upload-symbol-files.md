# Using SendPdbs to Automatically Upload Symbol Files

Many customers automate the upload of Windows symbols and executables using the BugSplat utility SendPdbs.exe.

Each build of your product that is used to send crash reports must have an exact set of matching exe/symbol files uploaded to BugSplat. Typically you will provide a unique BugSplat application name/version for each build.

Feel free to send symbols to BugSplat for every build on your build/integration server. There is no limit on the total amount of symbols you can post to BugSplat. But by default, any single symbol file must be smaller than 2 GB and any single symbol store (identified by an application name and version) must be less than 8 GB. Enterprise customers can increase these limits.

Credentials can be provided to SendPdbs via the /u and /p command-line arguments. Alternatively, you can save these credentials in the Windows Credentials Manager. To use values from Windows Credential Manager, create a new Generic Credentials entry and provide the entry name as the value for the /credentials argument.

Credentials can also be provided using an Oauth2 client id/client secret created on our [integrations](https://app.bugsplat.com/v2/settings/database/integrations) page.  Just supply these values as the user name/password.

{% hint style="warning" %}
BugSplat will automatically remove unreferenced symbols in large symbol sets. If your database contains more than 5 gigabytes of symbol data, our cleanup algorithm will automatically remove symbols that haven't been referenced by a crash report in more than 90 days. Additionally, newly posted symbols not referenced by a crash report within 15 days will be removed.
{% endhint %}

You can download the BugSplatSendPdbs file by [clicking here](https://app.bugsplat.com/browse/download\_item.php?item=sendpdbs).

Running SendPdbs.exe in a command window without any arguments show the following usage information:&#x20;

```
 USAGE: This utility searches your build tree for Windows symbol/executable
  files and uploads them to the BugSplat server.

    Specify BugSplat credentials using either:
    /u USER
    /p PASSWORD
    -or-
    /credentials WINDOWS-CREDENTIAL-MANAGER-GENERIC-CREDENTIALS

  Other required parameters:
    /b DATABASE  BugSplat database
    /a APPNAME   AppName for the symbol files (e.g. /a MyApp)
    /v VERSION   Version number of AppName (e.g. /v 1.0), required if /l not set
    /l LIBRARY   Library from which to extract file version info (e.g. /l MyApp.exe), required if /v not set

  Upload is the default action, modify using the following parameters:
    /s           Search subdirectories, default off
    /d PATH      Folder to search, defaults to current folder
    /f FILESPEC  Files to search, defaults to "*.pdb;*.dll;*.exe"
    /compression LEVEL  Optimal, fastest, or nocompression, defaults to Optimal
    /threads NUM Threads used during upload, defaults to 20
    /c RETRIES   Maximum retries to attempt for each file upload, defaults to 3.

  To remove symbols from BugSplat use the following single parameter:
    /r           Removes all symbols for specified AppName, AppVersion, Database

  The log level can be modified with:
    /verbose     Use for additional diagnostics
```
