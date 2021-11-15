---
description: Previously known as sub keys, or subkeying
---

# Crash Grouping Concepts

BugSplat groups related crashes together to make analysis easier. You can see the crashes reported by groups on our [Summary](https://app.bugsplat.com/v2/summary) page. The name of each crash group corresponds to the stack frame used to group the crashes. By default, crashes are grouped using the top-level frame of the call stack. &#x20;

The default crash grouping works well a lot of the time. However, most customers run into cases where the top of the call stack is a shared function. For example, perhaps it's an operating system function, and the interesting bits of the call stack exist at a lower level in your application code. In this case, BugSplat offers you additional ways to group your crashes.

The diagram below shows an example call stack hierarchy which we will use in the following discussion. There are four unique call stacks in this example, all sharing the top-level stack frame 'A'. The call stacks have been labeled 1-4 in the diagram below. Let's inspect call stack 2. This call stack is the function call sequence 'X-F-B-A'. That is, the program terminated during the execution of function A. The sequence of calls leading to the crash is: X called F, which called B, which called A.

```
            An example call stack tree
           
 Level 1              A
                    /   \
 Level 2           B     C
                  / \     \
 Level 3         C   F     G
                /   / \     \
 Level 4       D   X   Y     H
 
 Call stack    1   2   3     4
```

#### Group crashes using group-by-level&#x20;

BugSplat has two different crash grouping mechanisms available. The first is **group-by-level**. This mechanism will automatically group all crashes with the same top-level stack frame at some lower level of each call stack. Using level 2 to group our example call stack tree above, there would be two new crash groups shown on the summary page with names 'B' and 'C'. Crash group 'B' would contain crashes with call stacks 1, 2, and 3. Crash group 'C' would contain crashes with call stack 4. &#x20;

When you define a crash group, all new crashes will be processed according to its rules. In our example, all new crashes with 'A' at the top of the call stack would be shown grouped by 'B' or 'C'.  If a new call stack were to generate a crash in function A, it too would be grouped at level 2.

{% hint style="warning" %}
You can only define a single group-by-level for any top-level stack frame. When you define a group-by-level, any existing crash groups for the top-level stack frame are removed.
{% endhint %}

Crash group-by-level is powerful and operates on all crashes related to the same top-level stack frame, but group-by-level is a blunt tool that doesn't work for every situation. What if some crashes don't have your interesting** **code at the selected level? If you want to do more surgical crash grouping, you need **group-similar.**

#### Group Similar Crashes&#x20;

The **group-similar** feature groups crashes that share a number of stack frames beginning at the top of the call stack. In our example above, if we used group-similar at level 3 on call stack 2, a new crash group would be created for stack frame 'F' and crashes in both call stack 2 and 3 would be included in the 'F' grouping.

You can have any number of group-similar crash groups defined for a top-level stack frame. If a call stack matches multiple crash groups, it will be grouped using the deepest matching level.

{% hint style="info" %}
**Group-similar** and **group-by-level** can be used together! It's just fine to create a group-by-level crash group and refine its results using group-similar. &#x20;
{% endhint %}

Let's return to our example above. We first created a group-by-level crash group at level 2, which resulted in groups 'B' and 'C'. Next, we created a group-similar group for stack frame 'F'. The three groups we created will now be used for classifying crashes. Crashes with call stack 1 will be grouped in 'B', crashes with call stacks 2 or 3 will be grouped in 'F', and crashes with call stack 4 will be grouped in 'C'. No crashes will be grouped with the default top-level stack frame 'A'.

**Deleting Crash Groups**

Any individual crash group can be deleted. This applies to groups created with group-similar and for any single group that was automatically created with group-by-level.  You can also remove all crash groups for a single top-level stack frame.

**Grouping of Existing Crashes **

When you define a crash group, you select a timeframe for regrouping existing crashes in your database. The grouping for crashes older than the selected timeframe will not be modified. &#x20;
