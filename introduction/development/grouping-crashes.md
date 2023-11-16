# Grouping Crashes

## Overview

Deciding which crashes your team should focus on can seem daunting. For many applications, it's impossible to investigate every crash. Fortunately, BugSplat automatically groups similar crashes, allowing you to concentrate your efforts on the bugs causing the most instability.

BugSplat's [Summary](https://app.bugsplat.com/v2/summary) page is a table of crash groups. Usually, crash reports are grouped by the top stack frame when the application crashes. However, sometimes the top of a stack contains common code that causes unrelated crashes to be grouped together. When this happens, auto-group rules can automatically select a lower stack frame that more accurately groups related crashes.

### Auto-Group Rules

BugSplat has a set of customizable rules that skip over functions that are typically not interesting to application developers. Our default rules are designed to get you started quickly and can be modified as required for each crash report database.

The auto-group rules are pattern-based matching rules that skip irrelevant stack frames and create more meaningful crash groups. The three types of rules are **group by**, **group after,** and **ignore** frames. Rules are defined per platform and can be specified to match either the function or file portion of the call stack.

You can view and change Auto-Group rules on the [Settings/Grouping](https://app.bugsplat.com/v2/database/grouping) page.

Let's take a look at how BugSplat groups a report with the Windows OS function `KERNELBASE!RaiseException` at the top of the stack. Our default rule is shown below:

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption><p>Default Ignore Rule</p></figcaption></figure>

This rule, for Windows Native C++ crash types, **groups after** any stack frame where the function matches `KERNELBASE*`. When BugSplat processes reports containing `KERNELBASE!RaiseException`, the rule matches and crashes will automatically be grouped by the following frame of the call stack. Group after rules are useful for excluding frames that are known to be common error conditions. \\

Developers can also add rules that **group by** frames matching certain patterns. **Group by** rules are useful for including items that can be identified as belonging to your application. For example, you might choose **group by** to specify a file matching a path on your build machine, or a function matching your main application's module.

Auto-Group rules are matched via [glob patterns](https://en.wikipedia.org/wiki/Glob\_\(programming\)). The following table describes common glob patterns.

<table><thead><tr><th width="122">Pattern</th><th width="495">Description</th><th width="170">Example</th><th width="310">Matches</th><th width="224">Does not match</th></tr></thead><tbody><tr><td><code>*</code></td><td>matches any number of any characters including none</td><td>Law*</td><td>Law, Laws, or Lawyer</td><td></td></tr><tr><td><code>**</code></td><td>matches any number of path/directory segments. When used must be the only contents of a segment.</td><td>/**/some.*</td><td>/foo/bar/bah/some.txt, /some.txt, or /foo/some.txt</td><td></td></tr><tr><td><code>?</code></td><td>matches any single character</td><td>?at</td><td>Cat, cat, Bat or bat</td><td>at</td></tr><tr><td><code>[abc]</code></td><td>matches one character given in the bracket</td><td>[CB]at</td><td>Cat or Bat</td><td>cat or bat</td></tr><tr><td><code>[a-z]</code></td><td>matches one character from the range given in the bracket</td><td>Letter[0-9]</td><td>Letter0, Letter1, Letter2 up to Letter9</td><td>Letters, Letter or Letter10</td></tr><tr><td><code>[!abc]</code></td><td>matches one character that is not given in the bracket</td><td>[!C]at</td><td>Bat, bat, or cat</td><td>Cat</td></tr><tr><td><code>[!a-z]</code></td><td>matches one character that is not from the range given in the bracket</td><td>Letter[!3-5]</td><td>Letter1, Letter2, Letter6 up to Letter9 and Letterx etc.</td><td>Letter3, Letter4, Letter5 or Letterxx</td></tr></tbody></table>

Auto-group rules are processed in a specific, consistent order that cannot be changed. The rules engine follows the logic below:

* If there are any **group by** stack frame matches, select the top-most matching frame as the candidate frame. Use the resulting frame for grouping. Don't process any more rules.
* If there are any **group after** stack frame matches, select the lower-most matching frame as the candidate frame. Starting with this frame, skip over any frames that match the **ignore** rules until finding the first frame that isn't to be ignored. Use the resulting frame for grouping. Don't process any more rules.
* At this point, neither **group by** nor **group after** rules matched any stack frames. The rules engine will apply the **ignore** rules starting with the top stack frame, skipping over any frames that match the **ignore** rules until it finds the first frame that isn't to be ignored. The resulting frame is used for grouping.

{% hint style="info" %}
When you specify a new Auto-Group rule, it will be applied to newly processed and reprocessed crashes only. If you'd like to batch reprocess crashes to apply new rules, please reach out to [Support](mailto:support@bugsplat.com).
{% endhint %}

### Crashes Page

The [**Crashes**](https://app.bugsplat.com/v2/crashes) page displays a list of reports and their associated group under the **Stack Key** column. We've added a rule that effectively skips `KERNELBASE!RaiseException` and the reports are now grouped by the next frame in the stack `MyConsoleCrasher!_CxxThrowException(75)`. To see the report's stack trace, click the value in the **ID** column.

<figure><img src="../../.gitbook/assets/image (1) (2).png" alt=""><figcaption><p>Crash Page</p></figcaption></figure>

### Crash Page

On the **Crash** page, scroll down to the list of stack frames for the crashing thread. Notice that we didn't quite get our grouping rules correct. Our rules have caused the report to be grouped under the function in bold, `MyConsoleCrasher!_CxxThrowException`**.** The function we actually want to group on is `MyConsoleCrasher!ThrowByUser`. Expand the row containing `MyConsoleCrasher!ThrowByUser` to reveal the **Group Rules** button.

<figure><img src="../../.gitbook/assets/crash-expanded-frame.png" alt=""><figcaption><p>Crash Page with Stack Frame Expanded</p></figcaption></figure>

We can click **Group Rules** and add another rule that **groups after** stack frames where the function matches the glob `*_CXXThrowException*`. After creating this rule and reprocessing the crash report, you will see the correct grouping.

### Summary Page

Once new grouping rules have been applied, navigate to the [**Summary**](https://app.bugsplat.com/v2/summary) page to view an overview of groups in the selected database. The **Summary** page provides report counts for all of the various groups. Targeting groups with the highest report **Count** will generally give teams the best return on their efforts. Another interesting metric to target is **Users Affected,** which represents the number of unique users that ran into a specific problem during the selected time frame.

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption><p>Summary Page</p></figcaption></figure>
