# GitHub Issues

BugSplat's GitHub Issues integration allows your team to create defects from crash reports with only a few clicks. Pushing crash reports to GitHub Issues creates hyperlinks in BugSplat and allows for quick navigation from crash reports to your defect tracker and back again. Defects created from BugSplat automatically include symbolic call stack information and other crash-specific data.

### Integrating GitHub Issues with BugSplat

1. Generate a [Personal Access Token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens) (PAT) in GitHub. You may use a [classic PAT](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#personal-access-tokens-classic) or a [fine-grained PAT](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#fine-grained-personal-access-tokens), ensuring you set the correct **Resource Owner**, select the correct **Repository**, and add **Issues Read/Write** permissions.
2. [Login](https://app.bugsplat.com/cognito/login) to your BugSplat account.
3. Click the Gear icon (⚙️) in the top right of the page and navigate to the [Database Settings](https://app.bugsplat.com/v2/database/integrations) page and under **Database > Integrations >** **Defect Tracker > GitHub Issues** from the options shown.
4. Enter your GitHub **Username**, **API Token,** and **Repo Owner** into the appropriate boxes and click **Apply**.
5. Select your desired project.
6. Click **Apply** once more to complete the integration.

### Creating a defect in GitHub Issues from a BugSplat crash report

1\. Create a new defect from the crash page or a [stack key](../../../../education/bugsplat-terminology.md#stack-key) page by using the **Create Defect** button.

2\. Enter relevant details and click the **Submit** button to create the defect. You can also enter an existing **Defect ID** if you'd like to associate a crash or stack key with an existing issue from GitHub.

3\. Click the link to view the new GitHub issue created by BugSplat.

### Automated Defect Creation

Want to create issues in GitHub automatically for each new crash or crash group? Check out the document on auto-creating defects in your third-party defect tracker tool [here](automatic-issue-creation.md).
