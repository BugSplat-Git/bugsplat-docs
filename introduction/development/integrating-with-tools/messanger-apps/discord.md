# Discord

BugSplat’s Discord integration allows your team to receive alerts for new crashes and errors or new groups of crashes or errors. Receiving BugSplat alerts through Discord lets you stay on top of your application's stability and can serve as an early warning that something might be wrong.

## Integrating Discord with BugSplat <a href="#integrating-slack-with-bugsplat-docs" id="integrating-slack-with-bugsplat-docs"></a>

1. Create a new Discord webhook following the guide found [here](https://support.discord.com/hc/en-us/articles/228383668-Intro-to-Webhooks).
2. Login to [BugSplat](https://app.bugsplat.com/auth0/login) and navigate to the [Notifications ](https://app.bugsplat.com/v2/settings/database/notifications)page.
3. Select the Database for which you'd like to configure alerts
4. Under the **Discord** section, enter the webhook URL for your channel and click **Update**. More information regarding how to create a webhook URL can be found [here](https://support.discord.com/hc/en-us/articles/228383668-Intro-to-Webhooks).
5. Use the toggle buttons to set your notification preferences. You can choose to be notified for each new report or each new unique report group.
6. Use the **Fields** dropdown to select the fields you'd like to include in your notifications.

<figure><img src="../../../../.gitbook/assets/discord (1).gif" alt=""><figcaption><p>Configuring BugSplat's Discord Integration</p></figcaption></figure>
