# GitHub Issues

BugSplat's GitHub Issues integration allows your team to create defects from crash reports with only a couple clicks. Pushing crash reports to GitHub Issues creates hyperlinks in BugSplat and allows for quick navigation from crash reports to your defect tracker and back again. Defects created from BugSplat automatically include symbolic call stack information as well as other crash-specific data.

### Integrating GitHub Issues with BugSplat

1. [Login](https://app.bugsplat.com/auth0/login) to your account.
2. Go to the [Settings](https://app.bugsplat.com/v2/settings/database/integrations#defect-trackers) page and under the **Defect Tracker** tab select **GitHub Issues** from the drop-down menu.
3. Enter your GitHub **Username**, **API Token** and **Repo Owner** into the appropriate boxes and click **Apply**.
4. Select your desired project.
5. Click **Apply** once more to complete the integration.

### Creating a defect in GitHub Issues from a BugSplat crash report

1\. Create a new defect from the crash page or a [stack key](../../../../education/bugsplat-terminology.md#stack-key) page by using the **Create Defect** button.

2\. Enter relevant details and click the **Submit** button to create the defect. You can also enter an existing **Defect ID** if you'd like to associate a crash or stack key with an existing issue from GitHub.

3\. Click the link to view the new GitHub issue that was created by BugSplat.
