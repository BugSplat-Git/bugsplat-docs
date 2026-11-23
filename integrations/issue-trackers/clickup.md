# ClickUp

BugSplat allows your team to create tasks in [ClickUp](https://clickup.com/) with a few clicks. Our integration adds valuable crash data to your ClickUp tasks and links to the BugSplat dashboard so your team can quickly switch between BugSplat and the ClickUp lists where you track the rest of your work.

### Integrating ClickUp and BugSplat

1. In ClickUp, generate a [personal API token](https://developer.clickup.com/docs/authentication#generate-your-personal-api-token) by clicking your avatar and navigating to **Settings > Apps**. Personal tokens begin with `pk_`.
2. [Login](https://app.bugsplat.com/cognito/login) to your BugSplat account.
3. Click the Gear icon (⚙️) at the top right of the page and navigate to the [Database Settings](https://app.bugsplat.com/v2/database/integrations) page, and under **Database > Integrations >** **Defect Tracker**, select **ClickUp** from the options shown.
4. Enter your ClickUp token into the field labeled **API Token** and click **Update**.
5. Select your **Workspace** from the dropdown list and click **Update**.
6. Select the **Space** that contains the list you'd like to send tasks to and click **Update**.
7. Select a **List** and click **Update** again to complete the integration. Lists inside a folder are shown as **Folder / List**.

{% hint style="info" %}
BugSplat can only see the workspaces, spaces, and lists that the owner of the API token can access. Changing the workspace or space clears the selections below it, so you'll need to select a new space and list afterward.
{% endhint %}

To apply tags to every task BugSplat creates, choose **Tags** from the **Optional Fields** dropdown, click **Select**, enter a comma-separated list of tags, such as `bugsplat, crash`, and click **Update**.

### Creating a Task in ClickUp

1. Create a new defect for a crash group by using the **Create Defect** button on the crash page or a [Crash Group](../../introduction/development/using-the-app.md#crash-group-page) page.
2. Enter relevant details and click the **Submit** button to create the task. You can also enter an existing **Defect ID** to associate the crash group with an existing ClickUp task. BugSplat accepts a task ID, a `CU-` prefixed ID, or a task URL such as `https://app.clickup.com/t/86abc123`.
3. Click the link to view the new ClickUp task created by BugSplat. Linked tasks are labeled with ClickUp's `CU-<task id>` format.

The task's description includes links back to the crash group, crash details, and attachments in BugSplat, along with the application, version, error code, any notes or user feedback, and the symbolicated call stack. When you associate a crash group with an existing task, BugSplat appends this report to the end of the task's description rather than replacing it.

{% hint style="info" %}
Use the task's default ClickUp ID when linking an existing task. [Custom task IDs](https://help.clickup.com/hc/en-us/articles/6309958824727-Custom-Task-IDs) such as `DEV-123` aren't supported.
{% endhint %}

### Automated Defect Creation

Want to create tasks in ClickUp automatically for each new crash or crash group? Check out the document on auto-creating defects in your third-party defect tracker tool [here](automatic-issue-creation.md).
