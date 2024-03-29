# Fogbugz

Fogbugz integration with BugSplat crash reports allows your team to create defects with a single-button click. Hyperlinks allow quick navigation from defects to crash reports and back. Defects created from BugSplat automatically include symbolic call stack information and other crash-specific data.

### Integrating Fogbugz with BugSplat

1. Login to your account.
2. Click the Gear icon (⚙️) in the top right of the page and navigate to the Database Settings page, and under **Database > Integrations >** **Defect Tracker**, select **Frogbugz** from the options shown.
3. Enter your Fogbugz **Username** and your Fogbugz **URL** into the appropriate boxes and click **Apply**.
4. Select your desired **Project**.
5. Click **Apply**.

### Creating an issue in Fogbuz from a BugSplat crash report

1\. Create a new defect from the crash page or a [stack key](../../../../education/bugsplat-terminology.md#stack-key) page by using the **Create Defect** button.

2\. Enter relevant details and click the **Submit** button to create the defect. You can also enter an existing **Defect ID** if you'd like to associate a crash or stack key with an existing issue from FogBugz.

3\. Click the link to view the new FogBugz defect created by BugSplat.

### Automated Defect Creation

Want to create issues in Jira automatically for each new crash or crash group? Check out the document on auto-creating defects in your third-party defect tracker tool [here](auto-creating-defects-from-bugsplat-databases-in-attached-third-party-trackers.md).
