# Using the App

The BugSplat web application consists of three main pages: [Dashboard](https://app.bugsplat.com/v2/dashboard), [Crashes](https://app.bugsplat.com/v2/crashes), and [Summary](https://app.bugsplat.com/v2/summary).  These pages sort and organize an application's crash data, helping users quickly and simply navigate to the information they need.

Each of the main pages has subpages that provide more detail on individual [crashes](../../education/bugsplat-dictionary.md#crash-report), errors, [stack keys](../../education/bugsplat-dictionary.md#stack-key), and other important data.

## Dashboard

The [Dashboard](https://app.bugsplat.com/v2/dashboard) page is the application’s home and the place users land first inside the application. It provides users with an overview of how their application is performing and shows a list of the most recently submitted crashes.

![](../../.gitbook/assets/bugsplat-dashboard.png)

## Navigating with breadcrumbs

Users can always know where they are in the BugSplat app—and quickly navigate elsewhere—using the navigation breadcrumbs found in the top right of the screen. 

The breadcrumb goes as follows `Database / Application / Version / ID`

Applications, databases, and versions all provide searchable dropdowns to quickly find the desired input.  When shown as clickable, users can navigate to the database or version by clicking on the corresponding breadcrumb.

![](../../.gitbook/assets/navigating-with-breadcrumbs.gif)



## Crashes

The [Crashes](https://app.bugsplat.com/v2/crashes) page contains an unfiltered view of all an application's crashes organized with the most recent crashes at the top of the table.

![](../../.gitbook/assets/screen-shot-2021-07-16-at-1.03.48-pm.png)

From here, users manipulate the table in multiple ways to view specific sets of data. 

First, users can filter the crashes using either the [Search and Group By tools](grouping-and-aggregating-application-data.md) to find specific crashes. The “Timeframe” filter on the top right can also choose a specific time period.

Second, users can select different data types using the **Column Visibility** dropdown to change what data is visible.

Third, users can sort by ascending or descending order in a column by clicking on the header text of that column.

Fourth, users can click the right carrot \(arrow\) on each individual row to expand that row and view more details about that report.

![](../../.gitbook/assets/expando-row-crashes.gif)

Finally, if an individual crash seems important and worthy of further investigation, click on the numbered link in the ID column to navigate to the corresponding report.

## Reports

The **Reports** page allows for users to get information critical for understanding and fixing the defect which originally caused the issue. 

### User Details

The **User Details** modal contains high-level and 'human' information about a crash.  Use it to find out who crashed, when they crashed, and where they crashed \(IP address\).  This information is sometimes [obfuscated to protect users](../production/security-privacy-and-compliance/gdpr.md)—like it is in the image below.

![](../../.gitbook/assets/screen-shot-2021-07-16-at-3.08.42-pm%20%281%29.png)

### Crash Details 

The Crash Details modal provides information critical to understanding and triaging the crash.  Information like **Platform**, **Environment**, **Function Name and Line Number**, containing [**Stack Key**](../../education/bugsplat-dictionary.md#stack-key), and more.

![](../../.gitbook/assets/crash-details-modal.png)

### Active Thread

The **'Method'** column contains a list of methods in the stack trace of the thread that caused your program to crash. Depending on the [platform](https://www.bugsplat.com/docs/sdk) you may see methods that don't match your source code. This is because platforms such as [Windows Native](https://www.bugsplat.com/docs/faq/crash-details-active-thread), [.NET Framework](https://www.bugsplat.com/docs/sdk/dot-net), [Crashpad](https://www.bugsplat.com/docs/sdk/crashpad), [Breakpad](https://www.bugsplat.com/docs/sdk/breakpad), [OS X](https://www.bugsplat.com/docs/sdk/os-x), [Unity Native](https://www.bugsplat.com/docs/sdk/unity) and [Unreal](https://www.bugsplat.com/docs/sdk/unreal) require you to upload [symbols](https://www.bugsplat.com/docs/faq/symbols/) for each version you release in order to calculate the correct method names. For more information on how to upload symbols please see the documentation for your [platform](https://www.bugsplat.com/docs/sdk).

![](../../.gitbook/assets/active-thread-july-21.png)

Clicking the **&gt;** \(greater than symbol\) in the left-most column will expand the **Row Details** view.

The **Row Details** view will display the **Group Stacks** button that will allow you to create a [subkey](https://www.bugsplat.com/docs/faq/subkey/). Grouping at a different level of the call stack is called [subkeying](https://www.bugsplat.com/resources/development/subkeying/) and is useful in cases such as a crash that occurs in a 3rd party library, or when additional stack frames are added by a crash reporter.

Additionally, for [Windows Native](https://www.bugsplat.com/docs/faq/crash-details-active-thread) crashes the **Row Details** view will show a table of [Local Variables and Function Arguments.](https://www.bugsplat.com/resources/development/local-variables-function-arguments/)

The report page includes valuable information like crash time, environment, corresponding function name and line number, containing [stack key](../../education/bugsplat-dictionary.md#stack-key), and much more.

### Additional data

All data covered to this point on the Report page are found under the **Crash Overview** tab. To access additional information about crashes like **Other Threads**, **Registers**, **Modules**, **Debugger Output**, and **Attachments**—use the tabs found above the **User Details** module.

![](../../.gitbook/assets/viewing-tabs-crashreport%20%281%29%20%281%29.gif)

## Summary 

The [Summary](https://app.bugsplat.com/v2/summary) page shows a view of an application's crash data which automatically groups crashes by the top line of the call stack, allowing users to see which defects are occurring most frequently.  

You can group by other levels of the call stack as well to differentiate defects using [Subkeying](using-subkeying-to-find-difficult-crashes.md). 

The Summary page allows for similar table navigation as the [Crashes](using-the-app.md#crashes) page. Users can search, manipulate column size, alter visibility, and expand rows.

![](../../.gitbook/assets/summary-page.png)

The Summary page has a line chart at the top of the page which shows the top stack key by default, although you can select multiple stack keys to display on the chart.

![](../../.gitbook/assets/charting-stack-keys%20%283%29%20%283%29%20%283%29%20%281%29.gif)



## Stack Key

The Stack Key page shows a more in-depth view of all crashes found in an individual [stack key](../../education/bugsplat-dictionary.md#stack-key). 

The Stack Key page allows users to explore individual crashes, leave comments for team members, create defects based on the crash group, and more. 

Users can create [Custom Support Responses](../production/setting-up-custom-support-responses.md) to alert their end users to known issues identified with BugSplat. This is a very valuable tool for communicating with end users about known fixes or to point them toward specific helpful docs.



