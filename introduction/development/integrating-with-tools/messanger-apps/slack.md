# Slack

BugSplat’s Slack integration allows your team to receive alerts for new crashes and errors or new groups of crashes or errors. Receiving BugSplat alerts through Slack lets you stay on top of your application's stability and can serve as an early warning that something might be wrong.

{% hint style="warning" %}
**ATTENTION USERS OF BUGSPLAT'S** [**SLACK INTEGRATION**](https://docs.bugsplat.com/introduction/development/integrating-with-tools/messanger-apps/slack#integrating-slack-with-bugsplat-docs)

As part of our ongoing security work at BugSplat, we refreshed our Slack application's OAuth token on the morning of 17-July-2024.

If you use our [Slack notification integration](https://docs.bugsplat.com/introduction/development/integrating-with-tools/messanger-apps/slack#integrating-slack-with-bugsplat-docs), someone on your team may need to reauthorize it to ensure its continuing functionality.

Please feel free to contact [support@bugsplat.com](mailto:support@bugsplat.com) if you need more help.

Very best,

[![](https://changelog.bugsplat.com/\~gitbook/image?url=https%3A%2F%2Fdownloads.intercomcdn.com%2Fi%2Fo%2F1116483060%2F467bce4a9b8b2d564f60aaea%2Fbugsplat-team-signature.png%3Fexpires%3D1721228400%26signature%3D8d98ee6172f27aa416044ad496ef2bd650febb7decee6b550532f5fccaac8656\&width=300\&dpr=4\&quality=100\&sign=b9a63021\&sv=1)](https://downloads.intercomcdn.com/i/o/1116483060/467bce4a9b8b2d564f60aaea/bugsplat-team-signature.png?expires=1721228400\&signature=8d98ee6172f27aa416044ad496ef2bd650febb7decee6b550532f5fccaac8656)
{% endhint %}

#### Integrating Slack with BugSplat <a href="#integrating-slack-with-bugsplat-docs" id="integrating-slack-with-bugsplat-docs"></a>

1. Login to [BugSplat](https://app.bugsplat.com/cognito/login) and navigate to the [Notifications](https://app.bugsplat.com/v2/database/integrations#notifications)[ ](https://app.bugsplat.com/v2/database/notifications)page.
2. Select the Database for which you'd like to configure alerts
3. Under the **Slack** section, click the **Add to Slack** button.
4. You’ll be prompted to confirm your identity and choose which channel you would like the notifications posted to.
5. Select **Authorize**.
6. Use the toggle buttons to set your notification preferences. You can choose to be notified for each new report or each new unique report group.
7. Use the **Fields** dropdown to select the fields you'd like to include in your notifications.

<figure><img src="../../../../.gitbook/assets/slack.gif" alt=""><figcaption><p>Configuring BugSplat's Slack Integration</p></figcaption></figure>
