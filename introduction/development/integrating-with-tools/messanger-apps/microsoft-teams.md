# Microsoft Teams

BugSplat’s integration with Microsoft Teams allows your team to receive every new crash from a specific database as an alert or know when a new type of crash has occurred in a database.

These alerts let you stay on top of your crashes and can serve as an early warning that something might be wrong.

#### Integrating Microsoft Teams with BugSplat <a href="#integrating-slack-with-bugsplat-docs" id="integrating-slack-with-bugsplat-docs"></a>

1. Login to your [account](https://app.bugsplat.com/auth0/login).
2. Navigate to the [‘Notifications’ ](https://app.bugsplat.com/v2/settings/database/notifications)page.
3. Under Microsoft Teams, enter the Webhook URL for your channel and click **Update**. More information regarding how to create a webhook URL can be found [here](https://docs.microsoft.com/en-us/microsoftteams/platform/webhooks-and-connectors/how-to/connectors-using#setting-up-a-custom-incoming-webhook).
4. Use the toggle buttons to set your notification preferences. You can choose to be notified for each new crash or each new stack key.
