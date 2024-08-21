# Microsoft Teams

BugSplat’s Teams integration allows your team to receive alerts for new crashes and errors or groups of crashes or errors. Receiving BugSplat alerts through Teams lets you stay on top of your application's stability and can serve as an early warning that something might be wrong.

#### Integrating BugSplat with Microsoft Teams <a href="#integrating-slack-with-bugsplat-docs" id="integrating-slack-with-bugsplat-docs"></a>

{% embed url="https://bugsplat.wistia.com/medias/3742ugxbbw" %}
Teams Webhook Walkthrough
{% endembed %}

1. Create a new Teams webhook workflow following the guide found [here](https://support.microsoft.com/en-us/office/post-a-workflow-when-a-webhook-request-is-received-in-microsoft-teams-8ae491c7-0394-4861-ba59-055e33f75498).
2. Login to [BugSplat](https://app.bugsplat.com/cognito/login) and navigate to the [Notifications](https://app.bugsplat.com/v2/database/integrations#notifications) page.
3. Select the Database for which you'd like to configure alerts
4. Under the **Teams** section, enter the webhook URL for your channel and click **Update**.
5. Use the toggle buttons to set your notification preferences. You can be notified for each new report or unique report group.
6. Use the **Fields** dropdown to select the fields you want to include in your notifications.

<figure><img src="../../../../.gitbook/assets/teams.gif" alt=""><figcaption><p>Configuring BugSplat's Teams Integration</p></figcaption></figure>
