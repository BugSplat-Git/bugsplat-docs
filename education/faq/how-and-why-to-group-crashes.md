# How and why to Group Crashes

A [crash group](../bugsplat-terminology.md#crash-groups) lets you organize a set of call stacks using some level of the call stack hierarchy other than the top-most stack frame. Imagine you have the following three call stacks. By default, these would all be grouped using the top-most stack frame (stack key A), and you would have just one entry in the [Summary](https://app.bugsplat.com/v2/summary) page that grouped all three crashes.

A->B->C\
A->D->E\
A->F->G

If you set up a crash group at level 2, stack key A would no longer appear in the summary table. Instead, you would have three different groups displayed (B, D, and F).

To create a crash group, select a row of your call stack either on the Crash page or in our Stack Explorer, available from the Key Crash page. A group can be removed in a similar manner by selecting the top-most stack frame.

You can view the list of all your currently defined groups on the [Groups](https://app.bugsplat.com/v2/groups) page.

You can learn more about how crash grouping works [here](../how-tos/crash-grouping-concepts.md).
