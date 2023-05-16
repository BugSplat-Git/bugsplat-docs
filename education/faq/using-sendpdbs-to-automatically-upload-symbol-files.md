# Using SendPdbs to Automatically Upload Symbol Files

SendPdbs.exe is a BugSplat application used to automate the upload of Windows symbols and executables.  Each build of your product that sends crash reports must have an exact set of matching symbol files uploaded to BugSplat.&#x20;

Download SendPdbs from [here](https://app.bugsplat.com/browse/download\_item.php?item=sendpdbs).  The application is also included in the BugSplat Windows SDKs.

Feel free to send symbols to BugSplat for every build on your build/integration server. There is no limit on the total amount of symbols you can post to BugSplat. However, by default, any single symbol file must be smaller than 2 GB.

Uploading symbols with SendPdbs creates a 'symbol store,' which is just a group of symbols identified by their application name and version.  BugSplat will automatically remove symbol stores that haven't been accessed recently.  See below for those rules.  Using our web application, you can manually delete a symbol store.

{% hint style="warning" %}
BugSplat will automatically remove unreferenced symbols in large symbol sets. If your database contains more than 5 gigabytes of symbol data, our cleanup algorithm will automatically remove symbols that haven't been referenced by a crash report in more than 90 days. Additionally, newly posted symbols not referenced by a crash report within 15 days will be removed.
{% endhint %}

Older versions of SendPdbs (prior to version 4.0.0.0) required the application name and version used with SendPdbs to match the application name and version used to initialize BugSplat in your code.   This restriction has been lifted, and any symbols uploaded to your database are now available to all crash reports in that database.

Credentials can be provided to SendPdbs via the /u and /p command-line arguments. Alternatively, you can save these credentials in the Windows Credentials Manager. To use values from Windows Credential Manager, create a new Generic Credentials entry and provide the entry name as the value for the /credentials argument.

Credentials can also be provided using an OAuth2 client id/client secret created on our [OAuth Integrations](https://app.bugsplat.com/v2/settings/database/integrations#oauth) page.  Just supply these values as the username/password.

Running SendPdbs.exe in a command window without any arguments shows the following usage information:&#x20;

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

## Improving Upload Speeds

Customers located far away from our US-East hosting location, especially those with high-latency and high-bandwidth connections, sometimes report slow upload speeds.  We have several reports of significantly faster uploads after following the advice in the Microsoft technical note: [https://docs.microsoft.com/en-us/troubleshoot/windows-server/networking/tcpip-performance-known-issues](https://docs.microsoft.com/en-us/troubleshoot/windows-server/networking/tcpip-performance-known-issues)

