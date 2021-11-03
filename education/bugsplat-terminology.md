# BugSplat Terminology

### Call-Stack

These are the active subroutines of your program at some point in time. In the BugSplat world, that time is typically the time of a crash. The call stack identifies which subroutines have been called in what order. The call stack is also known as the stack trace, execution stack, program stack, or simply the stack.

### Crash Dialog

The **Crash Dialog** box is the end-user, customer-facing aspect of BugSplat. This box appears when an application configured to send crash data to BugSplat runs into a crash defect while in use. At this point, the dialog box prompts the end-user to provide an account of the events leading up to the crash as well as their name and email address.&#x20;

* [Adding your company or project's custom branding to the crash dialog box](how-tos/customize-the-crash-dialog.md).
* [Avoid collecting personally identifiable information through the crash dialog box](../introduction/production/security-privacy-and-compliance/avoid-collecting-personally-identifiable-information-pii.md).

### Crash Groups

Crash groups are used to group similar crashes and identify the root cause of multiple crash reports. Crash grouping helps speed up the analysis of specific crashes by ignoring uninteresting frames at the top of a call stack.  [Learn more here](how-tos/creating-crash-groups.md).

### Crash Report

This is the foundational unit of the BugSplat universe. BugSplat client libraries integrate with your code to capture exception information and send it to the BugSplat website. This package of information is called a crash report.

### Database

Databases are the highest order of organization inside of BugSplat.

Each database usually corresponds with a different product that your company offers. Some users have only one database, but larger teams and companies will generally find value in utilizing multiple databases to keep their crashes from different products (or versions of the same product) separate.

### Dump File

This file contains the program state such as call stack, register values, loaded modules and system information. In many environments, BugSplat creates a minidump file automatically at the time of a crash.

### End-Users

End-users are the people who experience and report crashes and errors. They have the option in BugSplat to leave their email and name when reporting crashes—although some BugSplat users choose to [obfuscate this information](../introduction/production/security-privacy-and-compliance/avoid-collecting-personally-identifiable-information-pii.md).

### Error Handler

This is the logic to mitigate an error scenario and resume program execution or to capture forensic data for post-mortem analysis and exit the program.

### Exception

This is an anomalous or exceptional condition requiring special processing during computation.

### Handled Exception

This is the exception that has logic to mitigate an error scenario such as to display a dialog to the user or reset values to their defaults. A program can continue execution after a handled exception.

### Key Crash

This is an abstract object that represents all crashes that share the same x stack frames from the stack key to the top of the call stack. This may be a BugSplat-specific construct.

### Stack Frame

This is one level of the call stack.

### Stack Key

Stack Key is a BugSplat specific term for the function name and line number that is used to group crashes on the [Summary](https://app.bugsplat.com/v2/summary) page.&#x20;

By default, the Stack Key is the function name and line number at the top of a stack of the crashing thread.&#x20;

You can create a [Subkey](../introduction/development/using-subkeying-to-find-difficult-crashes.md) to group the call stack at a different level of the crashing thread. Subkeying is useful for regrouping crashes that would otherwise be grouped by uninteresting stack frames such as system calls or third-party code. For more information about creating Subkeys, please see this [article](../introduction/development/using-subkeying-to-find-difficult-crashes.md).

### Subkey

Subkey is a now-retired term for a [Crash Group](bugsplat-terminology.md#crash-groups).&#x20;

### Symbols

These are files that contain information to map information in the minidump file to file names and line numbers in source code.

### Users

Users are the developers, QA professionals, support engineers, and others who use BugSplat to track user defects and application performance via the BugSplat web app. You are likely a “user.”

### Unhandled Exception

This is the unexpected exception where the surrounding code does not have the logic to handle the scenario and can put the program into an unknown state.

### Version

This is a version you can easily separate releases of your code inside of BugSplat. Typically, users will create a new version for each new release.

Using versions is a useful way to track crashes that you want to keep separate. Many users find value in comparing and tracking crash rates, top crashes, and particularly tricky-to-fix crashes across multiple versions

###

###
