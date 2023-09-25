# Jira

BugSplat's Jira integration allows your team to create defects with a couple clicks. Links from BugSplat to Jira allows for quick navigation from defects to crash reports and back. Defects created from BugSplat automatically include symbolic call stack information as well as other crash-specific data.

### Integrating Jira with BugSplat

1. Login to your account.
2. Go to the [Settings](https://app.bugsplat.com/v2/settings/database/integrations#defect-trackers) page and under **Defect Tracker** select **Jira** from the drop-down menu.
3. Enter your **Username**, **Password**, **URL**, and **IssueType** into the appropriate boxes.
4. Click **Apply**.
5. Once a successful connection to Jira has been established, you will be able to select one of your projects from the project dropdown list.
6. After selecting your desired project, click **Apply** again.
7. Add additional data in any of the other fields.

{% hint style="info" %}
**Assignee ID**: The optional 'Assignee ID' field requires the Atlassian user id when using Jira Cloud. Jira Server doesn't support user ids, so use the user name in that case.
{% endhint %}

### Creating an issue in Jira from a BugSplat crash report

1\. Create a new defect from the crashes page or a [stack key](../../../../education/bugsplat-terminology.md#stack-key) page by using the **Create Defect** button.

2\. Enter relevant details and click the **Submit** button to create the defect. You can also enter an existing **Defect ID** if you'd like to associate a crash or stack key with an existing defect from Jira.

3\. Click the link to view the new Jira issue that was created by BugSplat.

![](../../../../.gitbook/assets/creating-defect.gif)

{% hint style="info" %}
**Want to create issues automatically?** You can!  Please check out the document on auto-creating defects in your third-party defect tracker tool here --> [#creating-an-issue-in-jira-from-a-bugsplat-crash-report](jira.md#creating-an-issue-in-jira-from-a-bugsplat-crash-report "mention")
{% endhint %}
