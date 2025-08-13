---
description: How symbol files are automatically removed
---

# Automatic Symbol Cleanup

BugSplat will automatically remove symbols that haven't been recently accessed.  Symbols consume storage space in your account, so removing unused symbols can help reduce storage costs.  For many customers, our automatic rules need no further explanation.  However, if your symbol storage space is large and you are interested in optimizing costs, read on!

Once a database contains more than 5 gigabytes of symbol data, our cleanup algorithm will automatically remove symbols that have not been referenced recently.  In addition to automatic cleanup, you can also manually delete symbol stores using our web application.

Note that symbol files that are shared between [symbol stores](../../../education/bugsplat-terminology.md#symbol-store) (i.e., have been uploaded multiple times) will only be deleted when no symbol store references the file.

**Cleanup Rules**

1. Symbol stores not referenced by a crash report in more than 90 days will be removed.&#x20;
2. New symbol stores not referenced by a crash report within 15 days will be removed.
3. Individual symbol files not accessed in 90 days will be removed.

{% hint style="info" %}
Avoid uploading common symbols with every build. Library symbols that change infrequently should be placed in a dedicated symbol store rather than being re-uploaded with each new build of your application. This allows the 15-day "new symbol store" rule to remove individual builds that aren't referenced and ensures more efficient cleanup.
{% endhint %}
