# Why Group Crashes?

A [crash group](../bugsplat-terminology.md#crash-groups) lets you organize a set of call stacks using some level of the call stack hierarchy other than the top-most stack frame. Imagine you have the following three call stacks. By default, these would all be grouped using the top-most stack frame (stack key A), and you would have just one entry in the [Summary](https://app.bugsplat.com/v2/summary) page that grouped all three crashes.

A->B->C\
A->D->E\
A->F->G

If you set up a rule to ignore stack key A, crashes would instead be grouped at the second level of the call stack. Instead of a single group at A, you would have three different groups displayed (B, D, and F).

Crash report grouping rules can be changed on the [Groupings](https://app.bugsplat.com/v2/database/grouping) page or by expanding a stack frame on the **Crash** page and selecting **Group Rules.**&#x20;

For more information on how to group crash reports, please see [Grouping Crashes](../../introduction/development/grouping-crashes.md).

