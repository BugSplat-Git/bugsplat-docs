---
description: Testing Java crashes with the sample application myJavaCrasher
---

# myJavaCrasher

Before you enable your Java application with BugSplat technology, you may want to take a moment to examine the crash reports created with our sample applications.

To do this, login to BugSplat with user name ‘fred@bugsplat.com’ and password ‘Flintstone’.

All the crash reports in the fred@bugsplat.com account are actual crashes created with one of our sample applications. Crash reports from the application MyJavaCrasher are mixed in with crash reports from Windows applications.

To view just the Java application crashes you can filter on application name “MyJavaCrasher” on the [Crashes](https://app.bugsplat.com/v2/allcrash) page.

**Testing the sample application ‘MyJavaCrasher’**

Locate the MyJavaCrasher java source file from the zip that you just downloaded. Notice that there is a folder called “lib” which contains 4 libraries:

* `activation.jar`
* `mailapi.jar`
* `soap.jar`
* `bugsplat.jar`

You will need to add the lib folder to your `CLASSPATH`.

Build the MyJavaCrasher source file and launch it without making any changes. Optionally, you may choose to build MyJavaCrasherConsole,which is a console version of the sample \(for server applications\).

Many of the buttons on the dialog will cause a crash. When a crash occurs, fill out the requested information and select ‘Send’. You should see a corresponding report within a few seconds on the [Crashes](https://app.bugsplat.com/v2/allcrash) page.  
.

Next, we will change the sample app so that crash reports are sent to your BugSplat account.

In MyJavaCrasher.java search for the following line: `BugSplat.Init(“Fred”, “MyJavaCrasher”, “1.0″);`

Make the following changes:

* Modify the database name string “Fred” so that it matches your BugSplat database name.
* The BugSplat database is created and selected on the [Company](https://app.bugsplat.com/v2/company) page.
* Change the application name “MyJavaCrasher” to a name of your choice \(this is an arbitrary string, used to distinguish your applications across crash reports\).
* Change the version from “1.0″ to a string of your choice. Again, this is arbitrary, but would typically match the version and build string of your application.
* Compile the MyJavaCrasher source file.

Next, launch MyJavaCrasher and select a crash button. Fill in the requested information in the crash dialog and select “Send Report”

Finally, to verify that the report was sent, log into BugSplat as yourself \(not fred@bugsplat.com\) and verify the crash was recorded on the [Crashes](https://app.bugsplat.com/v2/allcrash) page

![](https://www.bugsplat.com/assets/img/brands/crash-dialogs/bugsplat-dialog.png)

