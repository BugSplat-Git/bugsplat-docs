---
description: >-
  Testing Windows Native C++ crashes with the sample application
  'MyConsoleCrasher'
---

# MyConsoleCrasher (C++)

Before you enable BugSplat in your Windows C++ application, you may want to take a moment to experiment with our `MyConsoleCrasher` sample application.

To get started, download the BugSplat SDK for Windows by clicking [here](https://app.bugsplat.com/browse/download_item.php?item=native). Download and unzip the contents of `BugSplat.zip`. After extracting the downloaded SDK, open the `MyConsoleCrasher.sln` file with Visual Studio.

1. Open `MyConsoleCrasher.sln` with Visual Studio 2022+
2. Define values for `BUGSPLAT_DATABASE`, `APPLICATION_NAME`, and `APPLICATION_VERSION` in `Samples\MyConsoleCrasher\MyConsoleCrasher.h`
3. Create a Client ID and Client Secret pair for your BugSplat database on the [Integrations](https://app.bugsplat.com/v2/settings/database/integrations) page
4. Create a file `Samples\MyConsoleCrasher\Scripts\env.ps1` and populate it with the following (being sure to substitute your `your-client-id` and `your-client-secret` values from the previous step):

```powershell
$BUGSPLAT_CLIENT_ID = "your-client-id"
$BUGSPLAT_CLIENT_SECRET = "your-client-secret"
```

5. Set the command-line arguments for the project to `/MemoryException`, or one of the other supported arguments from the sample's source code, to test various crashes. To set the command-line arguments, right-click the `MyConsoleCrasher` project and select **Properties > Debugging > Command Arguments**.

<figure><img src="../../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

6. Rebuild the project and run it outside of the Visual Studio debugger (Ctrl+F5). This is important since the debugger interferes with the BugSplat library’s exception handling. You should see a dialog such as that shown below:

![BugSplat Crash Dialog](<../../../../.gitbook/assets/bugsplat-crash-dialog (2) (2) (2) (2) (2) (2) (2) (2) (2) (2) (3) (2) (1) (2).png>)

7. Enter some descriptive text to help you identify the crash you are about to upload. Click the `Send Error Report` button, and voilà! The report will be sent! In the BugSplat web app, look for the crash report with the description you entered.
8. Navigate to the BugSplat [Dashboard](https://app.bugsplat.com/v2/dashboard) and click the link in the ID column to view details about your crash, including the full symbolicated stack trace and various crash metadata.

Finally, experiment with other features of the library by examining the `MyConsoleCrasher` source code and supplying different command-line arguments.

#### Additional Info

{% content-ref url="address-sanitizer-reports.md" %}
[address-sanitizer-reports.md](address-sanitizer-reports.md)
{% endcontent-ref %}
