# Finding missing reports

In some rare situations, you may be missing a crash report you know was sent to BugSplat, or you might notice a difference in the total crash volume shown on the Overview page versus what is reported on your [**Crashes**](https://app.bugsplat.com/v2/crashes) and [**Summary**](https://app.bugsplat.com/v2/summary) pages.

Before becoming alarmed, please note that this may happen naturally as part of our crash handling protocol.

### When does this happen?

1. Repeat crashes from one IP address.

   When we receive too many crashes from an individual IP we automatically reject some of them.

   In such a case, what is usually occurring is some kind of closed crash loop. To keep your crash counts reasonable, BugSplat limits incoming crash reports from a single IP address to one every several seconds. Even when we reject crash reports, the total volume on the Overview chart will reflect all incoming crashes. In this case, you will still be able to see many different examples of the crash, which will allow you to fix the problem.

2. When your application is experiencing exceptionally high crash volume.

   When your application is experiencing crash volume well above it’s normal level or well beyond the rate provided by your subscription, a percentage of the crash reports will be rejected.

   This high-volume rejection keeps unusual periods of crash activity from rapidly exhausting your BugSplat subscription. Again, BugSplat allows you to accurately see your overall volume on the Overview page, and you will still receive a statistically significant number of these crashes to view on the [**Crashes**](https://app.bugsplat.com/v2/crashes) and [**Summary**](https://app.bugsplat.com/v2/summary) pages.

3. Crashes submitted with a POST size larger than our 2mb limit.

   Our current POST size limit is 2mb. This is much bigger than the data BugSplat sends as part of the crash report, but if you are adding additional log files to your crash reports, please keep their total size under one megabyte to ensure that crash reports are not rejected.

4. Reports were from a retired version.

   If you are missing crash reports check to make sure they were not from a version you have previously retired. You can do this by visiting [app.bugsplat.com/versions/](https://app.bugsplat.com/v2/versions)

