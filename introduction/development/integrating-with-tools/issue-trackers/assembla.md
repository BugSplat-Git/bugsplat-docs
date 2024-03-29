# Assembla

BugSplat’s Assembla integration allows your team to create tickets in Assembla with as little as a few clicks. Hyperlinks allow quick navigation from defects to crash reports and back. Defects created from BugSplat automatically include symbolic call stack information and other crash-specific data.

### Integrating Assembla with BugSplat

1. Log into [Assembla](https://www.assembla.com/home)
2. Click the dropdown next to your picture in the top right corner. **Select Profile > API Applications and Sessions**.
3. Register a new application with Assembla and enter the following redirect uri `https://app.bugsplat.com/defectTracking/assembla-webhook.php?database={{database}}`. Be sure to replace the value of `{{database}}` with the name of your BugSplat database. Take note of the **Application ID** and **Application Secret** for the application you registered - you’ll need to provide these values in Step 6
4. Login to your BugSplat account and click on the Gear icon (⚙️) in the top right of the page and navigate to the Database Settings page, and under **Database > Integrations >** **Defect Tracker**,
5. Select **Assembla** from the options shown.
6. Enter your **Assembla URL** (https://your-site.assembla.com), **Application ID,** and **Application Secret,** and click **Authorize.**
7. You will be redirected to a page prompting you to allow your integration access to Assembla. Click **Allow**
8. You will be redirected back to the [Settings](https://app.bugsplat.com/v2/database/integrations#defect-trackers) page. When the page has loaded, select the desired **Assembla Space** and click **Update.**

### Creating a ticket in Assembla from a BugSplat crash report

1\. Create a new defect from the crash page or a [stack key](../../../../education/bugsplat-terminology.md#stack-key) page by using the **Create Defect** button.

2\. Enter relevant details and click the **Submit** button to create the defect. You can also enter an existing **Defect ID** if you'd like to associate a crash or stack key with an existing issue from Assembla.

3\. Click the link to view the new Assembla defect created by BugSplat.

### Automated Defect Creation

Want to create issues in Jira automatically for each new crash or crash group? Check out the document on auto-creating defects in your third-party defect tracker tool [here](auto-creating-defects-from-bugsplat-databases-in-attached-third-party-trackers.md).
