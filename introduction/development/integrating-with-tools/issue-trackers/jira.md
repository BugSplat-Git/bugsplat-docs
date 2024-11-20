# Jira

BugSplat's Jira integration allows your team to create defects with a few clicks. Links from BugSplat to Jira allow for quick navigation from defects to crash reports. Defects created from BugSplat automatically include symbolic call stack information and other crash-specific data.

### Integrating Jira and BugSplat

1. Login to your account.
2. Click the Gear icon (⚙️) at the top right of the page and navigate to the [Database Settings](https://app.bugsplat.com/v2/database/integrations) page, and under **Database > Integrations >** **Defect Tracker**, select **Jira** from the options shown.
3. Enter your **Username**, **Token**, **URL**, and **Issue Type** into the appropriate boxes. For Jira Cloud, use an [API Token](https://id.atlassian.com/manage-profile/security/api-tokens) in the **Token** field. For self-hosted Jira Server use your account's password for the **Token** field.
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

<figure><img src="../../../../.gitbook/assets/output (4).gif" alt=""><figcaption><p>Create a Jira Issue with BugSplat</p></figcaption></figure>

### Bi-Directional Status Syncing

BugSplat can listen to status updates on issues from Jira and update a defect group's status accordingly. Similarly, when a defect group's state is updated in BugSplat, the update can be pushed to Jira, which allows the two systems to stay synchronized.

To configure bi-directional Jira synchronization, perform the following steps:

1. Create a Jira System webhook via **⚙️ > System > Webhooks**
2. Generate a webhook secret and copy the secret value
3. Configure the webhook to emit events for **Issue Updated**
4. Navigate to BugSplat's [Defect Tracker Integration](https://app.bugsplat.com/v2/database/integrations#defect-trackers) page
5. Enter values for **Webhook Secret**, **Open Status**, and **Closed Status**

You can test the integration by linking a Jira issue to a BugSplat defect. Set the BugSplat status to Open or Closed. When the status changes in BugSplat, the Jira status should be set to the value you entered for **Open Status** and **Closed Status,** respectively.

### Automated Defect Creation

Want to create issues in Jira automatically for each new crash or crash group? Check out the document on auto-creating defects in your third-party defect tracker tool [here](auto-creating-defects-from-bugsplat-databases-in-attached-third-party-trackers.md).
