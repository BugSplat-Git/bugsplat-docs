# How BugSplat handles large crash volumes

### Built to scale

BugSplat is built entirely on AWS and is scalable to any application size or crash submission rate.  We process crashes for over 350+ installed applications around the world and have a [99.99%](http://stats.bugsplat.com/) application uptime. 

### How users handle crashes at scale inside the app

A great example of how BugSplat handles crashes is the [**Dashboard**](https://app.bugsplat.com/v2/dashboard) page. This tool provides a view of the stability of your application in an easy-to-use line graph, visually helping you see how your software is performing for users.

To get more detailed information on crashes, use the [**Summary**](https://app.bugsplat.com/v2/summary) page to see your crash data organized by their crash signature \(or StackKey\).

By looking at the Count column, you can see exactly how many times a particular crash defect caused your software to crash. You can then drill into that crash defect to inspect all the individual crash reports that are associated with the StackKey.

Because your crashes are automatically organized, you don’t have to waste time guessing which crash defects need your attention. Whatever Stack Key’s are near the top of your [**Summary**](https://app.bugsplat.com/v2/summary) page should absolutely command your team’s attention.

