# Monday.com

{% hint style="info" %}
BugSplat's Monday.com integration is in beta. Please message us if you want to request a feature or report a bug.
{% endhint %}

BugSplat allows your team to create items on [Monday.com](https://monday.com/) with a few clicks. Our integration adds valuable crash data to your Monday.com items and links to the BugSplat dashboard, so your team can quickly switch between looking at BugSplat and your Monday.com boards.

### Integrating Monday.com and BugSplat

1. Login to your account.
2. Click the Gear icon (⚙️) at the top right of the page and navigate to the [Database Settings](https://app.bugsplat.com/v2/database/integrations) page, and under **Database > Integrations >** **Defect Tracker**, select **Monday.com** from the options shown.
3. Enter your Monday.com [personal API token](https://developer.monday.com/api-reference/docs/authentication) into the field labeled **Token.**
4. Click **Update**.
5. Once you have connected to Monday.com, you can select one of your boards from the **Boards** dropdown list.
6. After selecting your desired board, click **Update** again.

### Push a new Item to Monday.com

1. Use the **Create Defect** button to create a new item on the crashes page or a crash group page to push a new item to Monday.com.
2. Enter relevant details and click the **Submit** button to create the defect.&#x20;
3. Click the link to view the new Monday.com item created by BugSplat.
4. View the items' updates to see that stack trace and other valuable info from BugSplat.

<figure><img src="../../../../.gitbook/assets/output (3).gif" alt=""><figcaption><p>Add BugSplat Item to Monday.com</p></figcaption></figure>

### Automated Defect Creation

Want to create issues in Monday.com automatically for each new crash or crash group? Check out the document on auto-creating defects in your third-party defect tracker tool [here](automatic-issue-creation.md).

