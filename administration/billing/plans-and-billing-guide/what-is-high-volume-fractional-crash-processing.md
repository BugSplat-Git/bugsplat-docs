# What is 'High Volume Fractional Crash Processing'?

In large-scale application environments, managing production crashes efficiently is crucial. For enterprise-grade projects, the volume of crashes can be overwhelmingly high, yet processing a random subset of these crashes can yield comprehensive insights without compromising data quality. This technique is particularly advantageous for BugSplat's enterprise customers, as it offers a balance between thorough crash analysis and cost-effectiveness, with pricing primarily influenced by crash volume.

For instance, if your application encounters 15 million crashes in a year, BugSplat's system allows for the configuration of a percentage-based, random selection of crashes for full processing. This means that out of the 15 million, a predetermined, random subset is processed, providing a reliable overview of your application's crash patterns, detailed insights into various crash categories, and a robust set of fully processed, searchable crash reports to facilitate your debugging and customer support processes.

Key Features of High Volume Fractional Crash Processing:

1. **Exclusively for Enterprise Customers**: This feature is specifically designed to address enterprise-level applications' high-volume crash analysis needs.
2. **Randomized, Configurable Crash Processing**: BugSplat can be set to randomly process a specific percentage of crashes from your production databases, ensuring a representative sample without the need to process every single crash.
3. **Billing Aligned with Processing**: Crash volume is billed at BugSplat standard enterprise rates, but you are billed only for the number of crashes processed.
4. **Visibility into Processing Mechanics**: The BugSplat web application will display fractional processing information so your team has visibility into the process.

This feature is crafted with the needs of developers and enterprises, aiming to provide a practical and cost-effective solution for managing and analyzing crash data at scale while maintaining data integrity and operational transparency.
