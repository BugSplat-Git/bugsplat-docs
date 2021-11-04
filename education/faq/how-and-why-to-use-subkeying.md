# How and why to use Subkeying

A subkey lets you organize a set of call stacks using some level of the call stack hierarchy other than the topmost stack frame. Imagine you have the following three call stacks. By default, these would all be grouped using the topmost stack frame (stack key A), and you would have just one entry in the [Summary](https://app.bugsplat.com/v2/summary) page that grouped all three crashes.

A->B->C\
A->D->E\
A->F->G

If you set up a sub key at level 2, stack key A would no longer appear in the summary table. Instead, you would have three different sub keys displayed (B, D, and F).

To create a subkey, select a row of your call stack either on an individual crash page or in our call stack explorer, available from the KeyCrash page. A subkey can be removed in a similar manner by selecting the top-most stackframe.

You can view the list of all your currently defined subkeys on the [Subkeys](https://app.bugsplat.com/v2/subkeys/) page.

You can learn more about how Subkeying works [here](../how-tos/crash-grouping-concepts.md).
