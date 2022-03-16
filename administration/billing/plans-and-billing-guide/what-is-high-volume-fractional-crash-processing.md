# What is 'High Volume Fractional Crash Processing'?

For many large applications, production crash volume is so big that processing a random percentage of crashes provides no loss of information, and doing so can lead to sizable cost savings as fewer crashes are fully processed.  This is useful as BugSplat enterprise plans are charged primarily based on crash volume.

**For example:** if your application crashes 50 million times per year, BugSplat can be configured to randomly select and fully process only 10% of those crashes.  This allows you to know exactly how often your app is crashing, collect statistically accurate information on crash groups, and still have plenty of searchable and fully-processed crash reports to use in your debug and support efforts.

**Details about High Volume Fractional Crash Processing:**

* Available for Business and Pro customers.
* Supports processing any percentage (1-99%) of crashes in your production databases&#x20;
* Crash volume is billed at BugSplat standard enterprise rates, but you are billed only for the number of crashes processed.
* The BugSplat web application will display fractional processing information so your team has visibility into the process.
