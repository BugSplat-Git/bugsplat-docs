# Troubleshooting

We try our best to make it easy to integrate with BugSplat. However, sometimes problems do arise, and not everything goes as planned. The following is a self-service troubleshooting guide that can help you fix common problems that occur when integrating with BugSplat.

## Missing Crashes

There are a few reasons why crashes might not make it to BugSplat. Below you will find some common reasons why crashes might be missing from BugSplat.

### The crashing program is running inside a debugger

Running your application inside of a debugger will block BugSplat's ability to intercept and upload the crash report. When testing your BugSplat integration ensure the debugger is not attached so that the crash report can be caught and uploaded by BugSplat.

### Your corporate internet has prevented BugSplat from upload

Corporate firewalls or network policies can prevent connections to BugSplat. Please ensure that the machine uploading the crash report can connect to BugSplat via https. BugSplat crash reports are uploaded to the domain .bugsplat.com where  is replaced with the name of your BugSplat database.

### The crash being uploaded is too large

By default BugSplat accounts are limited to posting crash uploads that are no larger than 20MB (compressed). Abnormally large minidump, log files, and/or other attachments can cause crash uploads to be larger than your account's crash upload size limit.  You can view BugSplat's [Errors](https://app.bugsplat.com/v2/errors) page to see if crash reports are being rejected due to size limit restrictions.

### Crashes are being posted too frequently

Occasionally BugSplat will reject crashes that are being posted in rapid succession. This is done to prevent BugSplat from being overrun as well as to allow customers to protect themselves from exceeding their subscription allotments. BugSplat may reject crash reports when they are incoming at a rate greater than 1,000,000 per month. Additionally, BugSplat can reject repeated crashes coming from the same IP address via a setting on the ‘[Settings](https://app.bugsplat.com/v2/settings/database/defect-tracker?)’ page. A list of rejected crashes can be found on BugSplat's [Errors](https://app.bugsplat.com/v2/errors) page.

### BugSplat has been configured incorrectly

Many of our sample programs are configured to post crashes to the Fred database in BugSplat. If you are using one of our samples to test BugSplat, ensure that the database you used to configure the BugSplat SDK matches the value of the database you're viewing in the BugSplat web application.

## Incorrect Call Stacks

In order to get call stacks that match the function names, file names, and line numbers in your application's source code, many of our integrations require uploading debug symbols. More information about symbol files can be found [here](../development/working-with-symbol-files/).

### Missing Symbols

You may see a red warning on certain crashes that indicate a crash is missing symbols. C++, .NET, Crashpad, macOS, iOS, and some JavaScript crashes might display this warning. For more information about uploading symbols please see the documentation that corresponds to your application's [platform](integrations/).

**Code Signing**

Code signing will change the timestamp of the executable being signed. If you are signing your executable be sure to upload the signed version of the executable to BugSplat so that our backend can load the correct executable in order to calculate the call stack.

### Anti-Cheat and Copy Protection

Applications that are protected by anti-cheat or copy protection software sometimes require additional software to be run on the BugSplat backend. BugSplat supports VM Protect but it must be configured by our support. Please contact [support@bugsplat.com](mailto:support@bugsplat.com) to configure unwinding VM Protect crash reports or to request support for other anti-cheat or copy protection solutions.

### Deleted Symbols

BugSplat does not limit the size of your uploaded symbols. However, we may automatically delete your symbols under certain circumstances. If your database has less than 5 GB of data, you won't be affected. Larger stores that have new symbol files uploaded, but not referenced by a crash within the first 15 days, will be deleted. Additionally, symbol stores for versions that haven't had a crash posted within the last 90 days will be deleted. For more information regarding symbol cleanup please see this [doc](../../education/faq/using-sendpdbs-to-automatically-upload-symbol-files.md).

## Symbol Size Limit Exceeded

In order to keep crash processing times low BugSplat limits both the size of a symbol store as well as the size of any individual file in a symbol store.

### Max File Size Limit Exceeded

By default BugSplat will reject a request to upload any symbol file that is larger than 2 GB. This is a soft limit that can be raised by our support team. If you need to support an application with a symbol file greater than 2 GB please contact [support@bugsplat.com](mailto:support@bugsplat.com).

**Table Max Size Limit Exceeded**

By default BugSplat will reject a request to upload symbols to a symbol store that will cause it to exceed a size of 4 GB. We reject symbol stores over 4 GB in order to encourage users to upload each build to a new symbol store (instead of uploading all builds to the same symbol store). You can create as many symbol stores as you like.
