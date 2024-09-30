# Finding Missing Reports

In some situations, you may be missing a crash report you know was sent to BugSplat, or you might notice a difference in the total crash volume shown on the Overview page versus what is reported on your [Crashes](https://app.bugsplat.com/v2/crashes) and [Summary](https://app.bugsplat.com/v2/summary) pages.

This may happen as part of our normal crash handling protocol.

### When does this happen?

1.  **Repeat crashes from one IP address.**

    When we receive too many crashes from an individual IP we automatically reject some of them.

    In such a case, what is usually occurring is some kind of closed crash loop. To keep your crash counts reasonable, BugSplat limits incoming crash reports from a single IP address to one every several seconds. Even when we reject crash reports, the total volume on the [Dashboard](https://app.bugsplat.com/v2/dashboard) chart will reflect all incoming crashes. In this case, you will still be able to see many different examples of the crash, which will allow you to fix the problem. Note, it's possible to limit repeated crashes over a longer timeframe using the Reject Repeated Crashes control on our options page.
2.  **When your application is experiencing exceptionally high crash volume.**

    When your application is experiencing crash volume well above its normal level or well beyond the rate provided by your subscription, a percentage of the crash reports will be rejected.

    This high-volume rejection keeps unusual periods of crash activity from rapidly exhausting your BugSplat subscription. Again, BugSplat allows you to see your overall volume accurately on the Overview page.
3.  **Crashes submitted with a POST size larger than our 20 MB limit.**

    Our current POST size limit is 20 MB. This is much bigger than the data BugSplat sends as part of the crash report, but if you are adding additional log files to your crash reports, please keep their total size under twenty megabytes to ensure that crash reports are not rejected.
4.  **Reports were from a retired version.**

    If you are missing crash reports check to make sure they were not from a version you have previously retired. You can do this by visiting the [Versions](https://app.bugsplat.com/v2/versions) page.
5. **Crash reports are missing values for the application name or version.** All BugSplat crash reports require an application name and version number.  If none of your crashes are accepted, check your integration to make sure these values are being supplied.&#x20;

