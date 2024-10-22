# Favro

{% hint style="info" %}
BugSplat's Favro integration is in beta. Please message us if you want to request a feature or report a bug.
{% endhint %}

BugSplat allows your team to create cards in [Favro](https://favro.com/) with a few clicks. Our integration adds valuable crash data to your Favro cards and links to the BugSplat dashboard so your team can quickly switch between looking at BugSplat and your Favro workflows.

#### Integrating Favro and BugSplat

1. Create an API token in Favro via **My Profile > API Tokens > Create New Token**
2. Navigate to the **⚙️ > Integrations > Defect Tracker** page in BugSplat
3. Select **Favro** and click the **Integrate** button
4. Enter your **Email** and **Token** and click **Update**
5. You should now see a list of organizations. Choose the correct organization and click **Update**
6. You should now see a list of widgets. Choose a widget and click **Update**
7. Optionally enter comma-separated tags in the **Tags** field and click **Update**

#### Push a New Item to Favro

1. To push a new card to Favro, use the **Create Defect** button on the **Crashes** or **Crash Group** pages.
2. Enter relevant details and click the **Submit** button to create the defect.
3. Click the link to view the new Favro card created by BugSplat.
4. View the card in Favro to see that stack trace and other valuable info from BugSplat.

#### Automated Defect Creation <a href="#automated-defect-creation" id="automated-defect-creation"></a>

Want to create cards in Favro automatically for each new crash or crash group? Check out the document on auto-creating defects in your third-party defect tracker tool [here](https://docs.bugsplat.com/introduction/development/integrating-with-tools/issue-trackers/auto-creating-defects-from-bugsplat-databases-in-attached-third-party-trackers).
