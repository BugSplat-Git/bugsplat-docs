# Crash Details Active Thread

The **'Method'** column contains a list of methods in the stack trace of the thread that caused your program to crash. Depending on the [platform](https://www.bugsplat.com/docs/sdk) you may see methods that don't match your source code. This is because platforms such as [Windows Native](https://www.bugsplat.com/docs/faq/crash-details-active-thread), [.NET Framework](https://www.bugsplat.com/docs/sdk/dot-net), [Crashpad](https://www.bugsplat.com/docs/sdk/crashpad), [Breakpad](https://www.bugsplat.com/docs/sdk/breakpad), [OS X](https://www.bugsplat.com/docs/sdk/os-x), [Unity Native](https://www.bugsplat.com/docs/sdk/unity) and [Unreal](https://www.bugsplat.com/docs/sdk/unreal) require you to upload [symbols](https://www.bugsplat.com/docs/faq/symbols/) for each version you release in order to calculate the correct method names. For more information on how to upload symbols please see the documentation for your [platform](https://www.bugsplat.com/docs/sdk).

![](https://www.bugsplat.com/assets/img/docs/crash-details-active-thread.png)

Clicking the **&gt;** \(greater than symbol\) in the left-most column will expand the **Row Details** view.

The **Row Details** view will display the **Group Stacks** button that will allow you to create a [subkey](https://www.bugsplat.com/docs/faq/subkey/). Grouping at a different level of the call stack is called [subkeying](https://www.bugsplat.com/resources/development/subkeying/) and is useful in cases such as a crash that occurs in a 3rd party library, or when additional stack frames are added by a crash reporter.

Additionally, for [Windows Native](https://www.bugsplat.com/docs/faq/crash-details-active-thread) crashes the **Row Details** view will show a table of [Local Variables and Function Arguments.](https://www.bugsplat.com/resources/development/local-variables-function-arguments/)

