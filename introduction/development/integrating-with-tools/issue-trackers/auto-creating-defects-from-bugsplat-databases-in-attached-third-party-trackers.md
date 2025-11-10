# Automatic Issue Creation for Defect Tracker Integrations

### Introduction

BugSplat provides the capability to auto-create issues in third-party issue trackers like [Jira](jira.md), [GitHub Issues](github-issues.md), [Azure DevOps](azure-devops.md), and [more](./) directly from your databases. Issues created from BugSplat contain rich debugging information to help your team prioritize and fix crashes. This document details the process of setting up this feature.

<figure><img src="../../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

#### Configuration

To set up the feature, follow these steps:

1. Navigate to the [Defect Tracker](https://app.bugsplat.com/v2/database/integrations) tab of the Database settings page
2. Follow the instructions to configure your [Defect Tracker](../#issue-trackers) if you haven't already
3. Enable the **New Crashes** or **New Groups** setting. Consider setting the **Group Threshold** value to ensure that new groups have multiple crashes before an issue is pushed to your Defect Tracker.
4. Save your settings.

Once these steps are completed, BugSplat will automatically create issues in your defect tracker according to the specified rules.

#### Options

There are two primary options for auto-creating defects:

1. **Creating a new linked issue with every new crash**: This option will generate a new issue in your linked third-party tracker for each new crash recorded in BugSplat.
2. **Creating a new linked issue for every new type of crash group:** This option generates a new issue for each unique type of crash group that is recorded. You can also set a threshold for the number of crashes in a group that are needed before an automatic issue is created. By setting this `Group Threshold` value to  `0`, an issue will be created for every new crash group.

#### Feedback and Feature Requests

We are continually expanding our integrations and will release additional features for third-party trackers in the near future. Your feedback helps us improve. Please send us your thoughts and suggestions at [support@bugsplat.com](mailto:support@bugsplat.com). We also welcome your requests for new features. In doing so, you can help us shape the future of BugSplat's third-party tracker integrations to better meet your needs.
