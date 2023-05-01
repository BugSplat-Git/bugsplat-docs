# BugSplat Terminology

### App

**Apps** are one level of how BugSplat organizes crash and error reports to keep them organized and easy to navigate for the teams working on them. &#x20;

It is best practice to keep back-end and client reports from a single Application separate. Using more than two 'apps' can provide for further organization and customization. &#x20;

### Call-Stack

These are the active subroutines of your program at some point in time. In the BugSplat world, that time is typically the time of a crash. The call stack identifies which subroutines have been called in what order. The call stack is also known as the stack trace, execution stack, program stack, or simply the stack.

### **Company**

The umbrella under which all [Databases](bugsplat-terminology.md#database) are organized.   Customizable inside of Settings at any time, name your company something recognizable to your overall organization (like your full company name). &#x20;

### Crash Dialog

The **Crash Dialog** box is the end-user, customer-facing aspect of BugSplat. This box appears when an application configured to send crash data to BugSplat runs into a crash defect while in use. At this point, the dialog box prompts the end-user to provide an account of the events leading up to the crash as well as their name and email address.&#x20;

* [Adding your company or project's custom branding to the crash dialog box](how-tos/customize-the-crash-dialog.md).
* [Avoid collecting personally identifiable information through the crash dialog bo](../introduction/production/security-privacy-and-compliance/avoid-collecting-personally-identifiable-information-pii.md)

### Crash Group

Similar crashes are grouped together by the stack frame where the error occurred and identify the root cause of multiple crash reports. The [**Summary**](https://app.bugsplat.com/v2/summary) page displays a list of crash groups. The **Key Crash** page displays a single crash group with a list of all the individual crashes belonging to the group.

By default, a Crash Group is identified by the function name and line number at the top of the stack of the crashing thread. However, you can [specify a list of rules](../introduction/development/grouping-crashes.md) that will automatically skip or group at specific [stack frames](bugsplat-terminology.md#stack-frame). Crash Grouping is useful for regrouping crashes that would otherwise be grouped by uninteresting stack frames such as system calls or third-party code. For more information about creating Crash Groups, please see [grouping crashes](https://docs.bugsplat.com/introduction/development/grouping-crashes).

### Crash Report

This is the foundational unit of the BugSplat universe. BugSplat client libraries integrate with your code to capture exception information and send it to the BugSplat website. This package of information is called a crash report.

### Database

Databases are the highest level of data organization inside of BugSplat.

Each database usually corresponds with a different product that your company offers. Some users have only one database, but larger teams and companies will generally find value in utilizing multiple databases to separate their crashes from different products (or versions of the same product).

### Dump File

This file contains the program state such as call stack, register values, loaded modules and system information. In many environments, BugSplat creates a minidump file automatically at the time of a crash.

### End-Users

End-users are the people who experience and report crashes and errors. They have the option in BugSplat to leave their email and name when reporting crashes—although some BugSplat users choose to [obfuscate this information](../introduction/production/security-privacy-and-compliance/avoid-collecting-personally-identifiable-information-pii.md).

### Error Handler

This is the logic to mitigate an error scenario and resume program execution or to capture forensic data for post-mortem analysis and exit the program.

### Exception

This is an anomalous or exceptional condition requiring special processing during computation.

### Handled Exception

This is the exception that has logic to mitigate an error scenario such as displaying a dialog to the user or resetting values to their defaults. A program can continue execution after a handled exception.

### Stack Frame

This is one level of the call stack.

### Subkey

Subkey is a now-retired term for a [Crash Group](bugsplat-terminology.md#crash-groups).&#x20;

### Symbols

Symbol files contain information to map information in a crash report to file names and line numbers in the source code.

### Users

Users are the developers, QA professionals, support engineers, and others who use BugSplat to track user defects and application performance via the BugSplat web app. You are likely a “user.”

### Unhandled Exception

This is the unexpected exception where the surrounding code does not have the logic to handle the scenario and can put the program into an unknown state, typically leading to an application exit.

### Version

Typically, companies will create a new version of their application for each new build.  This is especially helpful because symbol files are uploaded into a 'symbol store' identified by the database, application name, and application version.

Using versions is a useful way to track crashes that you want to keep separate. Many users find value in comparing and tracking crash rates, top crashes, and particularly tricky-to-fix crashes across multiple versions.

###

###
