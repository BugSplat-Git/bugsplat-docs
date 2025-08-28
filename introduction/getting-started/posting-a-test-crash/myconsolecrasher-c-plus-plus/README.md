---
description: >-
  Testing Windows Native C++ crashes with the sample application
  ‘myConsoleCrasher’
---

# myConsoleCrasher (C++)

Before you enable your native Windows application with BugSplat technology, you may want to take a moment to experiment with our `myConsoleCrasher` sample application.

To get started, download the BugSplat Microsoft Windows Native C++ SDK from the [Downloads](https://www.bugsplat.com/docs/sdk/) page. Once the contents of `BugSplatNative.zip` have been extracted, navigate to the `samples` folder and open the `myConsoleCrasher.vcxproj` file with Visual Studio.

1. Open myConsoleCrasher.vcxproj with Visual Studio 2019+&#x20;
2. Define values for `BUGSPLAT_DATABASE`, `APPLICATION_NAME`, and `APPLICATION_VERSION` in MyConsoleCrasher\MyConsoleCrasher.h
3. Create a Client ID and Client Secret pair for your BugSplat database on the [Integrations](https://app.bugsplat.com/v2/settings/database/integrations) page
4. Create a file `MyConsoleCrasher\Scripts\env.ps1` and populate it with the following (being sure to substitute your `{{id}}` and `{{secret}}` values from the previous step):

```
$BUGSPLAT_CLIENT_ID = "{{id}}"
$BUGSPLAT_CLIENT_SECRET = "{{secret}}"
```

5. Check the permissions of MyConsoleCrasher\Scripts\SendPdbs.ps1.  Make sure this file is not blocked for execution.

<figure><img src="../../../../.gitbook/assets/image (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

6. Rebuild the project and run it outside of the Visual Studio debugger (Ctrl+F5). This is important since the debugger interferes with the BugSplat library’s exception handling. You should see a dialog such as that shown below:

![BugSplat Crash Dialog](<../../../../.gitbook/assets/bugsplat-crash-dialog (2) (2) (2) (2) (2) (2) (2) (2) (2) (2) (3) (2) (1) (2).png>)

Enter some descriptive text to help you identify the crash you are about to upload.  Click the `Send Error Report` button, and voilà!  The report will be sent! On the BugSplat website, look for the crash report with the description you entered.

Finally, experiment with other features of the library by examining the `myConsoleCrasher` source code and supplying different command-line arguments.

## Further Information

{% content-ref url="address-sanitizer-reports.md" %}
[address-sanitizer-reports.md](address-sanitizer-reports.md)
{% endcontent-ref %}
