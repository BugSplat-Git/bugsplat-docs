# Azure DevOps

Visual Studio Team Services (Azure DevOps) integration with BugSplat allows your team to create defects with a few clicks. Hyperlinks allow quick navigation from defects to crash reports and back. Defects created from BugSplat automatically include symbolic call stack information and other crash-specific data.

### Integrating Azure DevOps with BugSplat

1. Login to your BugSplat account.
2. Click the Gear icon (⚙️) in the top right of the page and navigate to the Database Settings page, and under **Database > Integrations >** **Defect Tracker**, select **Azure DevOps** from the options shown.
3. Enter your **Azure DevOps URL** and **Personal Access Token** into the appropriate fields.
4. Click **Apply**.
5. If the connection is successful, you can select your desired project from the **Project** dropdown list.
6. Click **Apply**.

### Creating an issue in Azure DevOps from a BugSplat crash report

1\. Create a new defect from the crash page or a [stack key](../../../../education/bugsplat-terminology.md#stack-key) page by using the **Create Defect** button.

2\. Enter relevant details and click the **Submit** button to create the defect. You can also enter an existing **Defect ID** if you'd like to associate a crash or stack key with an existing issue from Azure DevOps.

3\. Click the link to view the new Azure DevOps defect created by BugSplat.

### Automated Defect Creation

Want to create issues in Azure DevOps automatically for each new crash or crash group? Check out the document on auto-creating defects in your third-party defect tracker tool [here](auto-creating-defects-from-bugsplat-databases-in-attached-third-party-trackers.md).
