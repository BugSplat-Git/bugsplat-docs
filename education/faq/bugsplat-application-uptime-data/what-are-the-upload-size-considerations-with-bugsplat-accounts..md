# What are the upload size considerations with BugSplat accounts.

Crash reports uploaded to BugSplat are typically quite small. However, you can add additional data to each crash report, for example many customers include log files. If you add additional data to crash reports, be aware that your BugSplat subscription has crash report size limitations. The size limitations apply to the total size of the compress \(zipped\) crash report.

Crash uploads are limited to **2 MB**, this includes the size of the minidump file, which is typically less than **50k**.

BugSplat allows uploads up to **10 MB** for Enterprise level users.

