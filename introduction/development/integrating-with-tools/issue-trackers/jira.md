# Jira

BugSplat's Jira integration allows your team to create defects with a few clicks. Links from BugSplat to Jira allow for quick navigation from defects to crash reports and back. Defects created from BugSplat automatically include symbolic call stack information and other crash-specific data.

### Integrating Jira and BugSplat

1. Login to your account.
2. Click the Gear icon (⚙️) in the top right of the page and navigate to the Database Settings page, and under **Database > Integrations >** **Defect Tracker**, select **Jira** from the from the options shown.
3. Enter your **Username**, **Password**, **URL**, and **Issue Type** into the appropriate boxes.
4. Click **Apply**.
5. Once a connection to Jira has been established, you can select one of your projects from the project dropdown list.
6. After selecting your desired project, click **Apply** again.
7. Add additional data in any of the other fields.

{% hint style="info" %}
The optional **Assignee ID** field requires the Atlassian user ID when using Jira Cloud. Jira Server doesn't support user IDs, so use the user name.
{% endhint %}

### Creating an Issue in Jira

1\. Create a new defect from the crashes page or a [stack key](../../../../education/bugsplat-terminology.md#stack-key) page by using the **Create Defect** button.

2\. Enter relevant details and click the **Submit** button to create the defect. You can also enter an existing **Defect ID** if you'd like to associate a crash or stack key with an existing defect from Jira.

3\. Click the link to view the new Jira issue created by BugSplat.

![Creating a Jira Issue from BugSplat](../../../../.gitbook/assets/creating-defect.gif)

### Automated Defect Creation

Want to create issues in Jira automatically for each new crash or crash group? Check out the document on auto-creating defects in your third-party defect tracker tool [here](auto-creating-defects-from-bugsplat-databases-in-attached-third-party-trackers.md).
