# Azure DevOps

Visual Studio Team Services (Azure DevOps) integration with BugSplat allows your team to create defects with a single-button click. Hyperlinks allow quick navigation from defects to crash reports and back. Defects created from BugSplat automatically include symbolic call stack information as well as other crash-specific data.

### Integrating Azure DevOps with BugSplat

1. Login to your BugSplat account.
2. Go to the [Settings](https://app.bugsplat.com/v2/settings/database/integrations#defect-trackers) page and under the **Defect Tracker** tab select **Azure DevOps** from the drop-down menu.
3. Enter your **Azure DevOps URL** and **Personal Access Token** into the appropriate fields.
4. Click **Apply**.
5. If the connection is successful, you will now be able to select your desired project from the **Project** dropdown list.
6. Click **Apply**.

### Creating an issue in Azure DevOps from a BugSplat crash report

1\. Create a new defect from the crash page or a [stack key](../../../../education/bugsplat-terminology.md#stack-key) page by using the **Create Defect** button.

2\. Enter relevant details and click the **Submit** button to create the defect. You can also enter an existing **Defect ID** if you'd like to associate a crash or stack key with an existing issue from Azure DevOps.

3\. Click the link to view the new Azure DevOps defect that was created by BugSplat.

{% hint style="info" %}
**Want to create issues automatically?** You can!  Please check out the document on auto-creating defects in your third-party defect tracker tool here --> [#creating-an-issue-in-jira-from-a-bugsplat-crash-report](azure-devops.md#creating-an-issue-in-jira-from-a-bugsplat-crash-report "mention")
{% endhint %}
