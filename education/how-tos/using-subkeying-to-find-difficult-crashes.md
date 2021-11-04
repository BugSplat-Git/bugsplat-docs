---
description: Previously known as sub keys, or subkeying
---

# Crash Grouping

At BugSplat, crashes are grouped together to make your analysis easier.   By default, crashes are grouped using the top-level of the call stack.  You can see the crashes reported by groups on our [Summary](https://app.bugsplat.com/v2/summary) page.  The name of each crash group corresponds to the stack frame used to group the crashes.

Crash grouping is useful when the default grouping contains a large number of crash reports with multiple code paths leading to a common crashing function.

When a crash happens in common code, such as a 3rd party library or system function,  the stack frames in application code are often more useful in determining where the crash originated. Crash grouping allows developers to group program crashes other levels of the call stack so that they more accurately target their fixes in order to maximize product stability.

Sorting program crashes brings visibility to problems customers are running into most often and allows developers to prioritize their fixes accordingly. For applications that generate large crash volumes, it may not be feasible to look at each crash individually. BugSplat’s [Summary](https://app.bugsplat.com/v2/summary) page provides an overview of the crash groups. Crash grouping allows teams to alter the default grouping rules to more accurately group crashes on the [Summary](https://app.bugsplat.com/v2/summary) page.

## Why is Subkeying Important?







The default crash grouping works well a lot of the time.  However, most customers run into cases where the top of the call stack is a shared function, for example perhaps it's an operating system function, and the interesting bits of the call stack exist at a lower level in your application code.  In this case, BugSplat offers you additional ways to group your crashes.

The diagram below shows an example call stack hierarchy which we will use in the following discussion.  There are four unique call stacks in this example, all sharing the top-level stack frame 'A'. The call stacks have been labeled 1-4 in the diagram below.   Let's inspect call stack 2.  This call stack is the function call sequence 'X-F-B-A'.  That is, the program terminated during the execution of function A.    The sequence of calls leading to the crash is:  X called F, which called B, which called A.

```
            An example call stack tree
           
 Level 1              A
                    /   \
 Level 2           B     C
                  / \     \
 Level 3         C   F     G
                /   / \     \
 Level 4       D   X   Y     H
 
 Callstack     1   2   3     4
```

#### Group crashes using group-by-level . (move this after Group Similar)

BugSplat has two different crash grouping mechanisms available.  The first is **group-by-level**.  This mechanism will automatically group all crashes with the same top-level stack frame at a lower level of each call stack.  Using level 2 to group our example call stack tree above, there would be two new crash groups shown on the summary page with names 'B' and 'C'.  Crash group 'B' would contain crashes with call stacks 1, 2, and 3.  Crash group 'C' would contain crashes with call stack 4. &#x20;

When you define a crash group, new crashes will be processed according to its rules.   So, in our example, all new crashes with 'A' at the top of the call stack would be shown grouped by 'B' or 'C'.  And, if some new call stack were to generate a crash in function A, it too would be grouped at level 2.&#x20;

Note that you can only define a single group-by-level for any top-level stack frame.  And, when you define a group-by-level, any existing crash groups for the top-level stack frame are removed.

Crash group-by-level is powerful.  It operates on all crashes related to the same top-level stack frame.  But it's a blunt tool too.  What if some crashes don't have your interesting** **code at the selected level?  If you want to do more surgical crash grouping, you need '**group-similar**'

#### Group Similar Crashes&#x20;

BugSplat's '**group-similar**' groups crashes that share a number of stack frames beginning at the top of the call stack.  For the example above, if we used group-similar at level 3 on call stack 2, a new crash group would be created for stack frame 'F'.  And crashes in both call stack 2 and 3 would be included in the 'F' grouping.

Group-similar and group-by-level can be used together!  It's just fine to create a group-by-level crash group and refine its results using group-similar.  You can have any number of group-similar crash groups defined for a top-level stack frame.  If a call stack matches multiple crash groups, it will be grouped using the deepest matching level.

Let's return to our example above.  We first created a group-by-level crash group at level 2, which resulted in groups 'B' and 'C'.  Then, we created a group-similar group for stack frame 'F'.  These three groups will now be used for classifying crashes.  Crashes with call stack 1 will be grouped in 'B',  crashes with call stacks 2 or 3 will be grouped in 'F', and crashes with call stack 4 will be grouped in 'C'.  No crashes will be grouped with the default top-level stack frame 'A'.

When you define a crash group, you can also select a timeframe for regrouping existing crashes in your database.  This feature allows you to group existing as well as new crashes.

##

##
