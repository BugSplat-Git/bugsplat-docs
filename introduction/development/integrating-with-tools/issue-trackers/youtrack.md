# YouTrack

YouTrack integration with BugSplat crash reports allows your team to create defects with a single-button click. Hyperlinks allow quick navigation from defects to crash reports and back. Defects created from BugSplat automatically include symbolic call stack information and other crash-specific data.

### Integrating YouTrack with BugSplat

1. [Login](https://app.bugsplat.com/cognito/login) to your account.
2. Click the Gear icon (⚙️) in the top right of the page and navigate to the Database Settings page, and under **Database > Integrations >** **Defect Tracker**, select **YouTrack** from the options shown.
3. Enter your **Username**, YouTrack **API Token**, YouTrack **URL**, and any additional options, and click **Apply**.

{% hint style="info" %}
You can find **Username**, **API Token**, and **URL** on the YouTrack Hub Integration page. [Click to learn more](https://www.jetbrains.com/help/youtrack/incloud/Manage-Permanent-Token.html).
{% endhint %}

4. Select your desired **Project**.
5. Click **Apply**.

### Creating a defect in YouTrack from a BugSplat crash report

1\. Create a new defect from the crash page or a [stack key](../../../../education/bugsplat-terminology.md#stack-key) page by using the **Create Defect** button.

2\. Enter relevant details and click the **Submit** button to create the defect. You can also enter an existing **Defect ID** if you'd like to associate a crash or stack key with an existing issue from YouTrack.

3\. Click the link to view the new YouTrack defect created by BugSplat.

### Automated Defect Creation

Want to create issues in Jira automatically for each new crash or crash group? Check out the document on auto-creating defects in your third-party defect tracker tool [here](automatic-issue-creation.md).
