# Microsoft Teams

BugSplat’s Teams integration allows your team to receive alerts for new crashes and errors or new groups of crashes or errors. Receiving BugSplat alerts through Teams lets you stay on top of your application's stability and can serve as an early warning that something might be wrong.

#### Integrating Microsoft Teams with BugSplat <a href="#integrating-slack-with-bugsplat-docs" id="integrating-slack-with-bugsplat-docs"></a>

1. Create a new Teams webhook following the guide found [here](https://learn.microsoft.com/en-us/microsoftteams/platform/webhooks-and-connectors/how-to/add-incoming-webhook#create-an-incoming-webhook).
2. Login to [BugSplat](https://app.bugsplat.com/cognito/login) and navigate to the [Notifications](https://app.bugsplat.com/v2/settings/database/integrations#notifications) page.
3. Select the Database for which you'd like to configure alerts
4. Under the **Teams** section, enter the webhook URL for your channel and click **Update**.
5. Use the toggle buttons to set your notification preferences. You can choose to be notified for each new report or each new unique report group.
6. Use the **Fields** dropdown to select the fields you'd like to include in your notifications.

<figure><img src="../../../../.gitbook/assets/teams.gif" alt=""><figcaption><p>Configuring BugSplat's Teams Integration</p></figcaption></figure>
