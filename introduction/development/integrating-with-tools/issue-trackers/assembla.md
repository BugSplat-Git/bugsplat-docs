# Assembla

BugSplat’s Assembla integration allows your team to create tickets in Assembla with as little as a few clicks. Hyperlinks allow quick navigation from defects to crash reports and back. Defects created from BugSplat automatically include symbolic call stack information as well as other crash-specific data.

### Integrating Assembla with BugSplat

1. Log into [Assembla](https://www.assembla.com/home)
2. Click the dropdown next to your picture in the top right corner. Select Profile > API Applications and Sessions.
3. Register a new application with Assembla and enter the following redirect-uri https://app.bugsplat.com/defectTracking/assembla-webhook.php?database={{database}}. Be sure to replace the value of {{database}} with the name of your BugSplat database. Take note of the Application ID and Application Secret for the application you registered - you’ll need to provided these values in step 6
4. Login to your BugSplat account and navigate to the Defect Tracker section on the [Settings](https://app.bugsplat.com/v2/settings/database/defect-tracker?)  page
5. Select Assembla from the drop down menu
6. Enter your Assembla URL (for instance https://your-site.assembla.com), Application ID and Application Secret and click Authorize
7. You will be redirected to a page prompting you to allow your integration access to Assembla, click Allow
8. You will be redirected back to the Options page. When the Options page has loaded select the desired Assembla space and click Update

### Creating a ticket in Assembla from a BugSplat crash report

1\. Create a new defect from an individual crash report page or a [stack key](../../../../education/bugsplat-terminology.md#stack-key) page by using the ‘Create a new defect’ link.

2\. Selecting the ‘Create defect’ link brings you to this page.

3\. Selecting the Submit button will create the defect.

4\. Go to Assembla to view your new issue based on your BugSplat crash report.
