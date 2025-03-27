# Webhook

BugSplat’s Webhook integration allows your team to configure custom notifications for each new report or group of reports. BugSplat's Webhook integration allows you to build integrations unique and novel integrations that can be tailor-fit to your company's workflow.

## Configuring a BugSplat Webhook <a href="#integrating-slack-with-bugsplat-docs" id="integrating-slack-with-bugsplat-docs"></a>

1. Login to [BugSplat](https://app.bugsplat.com/cognito/login) and navigate to the [Notifications](https://app.bugsplat.com/v2/database/integrations#notifications) page.
2. Select the Database for which you'd like to configure alerts.
3. Under the **Webhook** section, enter the URL of the webhook you would like to invoke and click **Update**.
4. Use the toggle buttons to set your notification preferences. You can be notified for each new report or unique report group.

Once configured, BugSplat will post an object that matches the following interface to your Webhook. Unlike other notifications, Webhook crash notifications have no maximum throughput limitation.

## Webhook Notification

<mark style="color:green;">`POST`</mark> `https://your-domain.com/path/to/your/webhook`

Notification payload for new BugSplat report or new BugSplat group. &#x20;

#### Request Body

| Name             | Type         | Description                                                                                      |
| ---------------- | ------------ | ------------------------------------------------------------------------------------------------ |
| notificationType | string       | <p>Type of notification:  "NewReport", or</p><p>"NewGroup"</p>                                   |
| id               | number       | BugSplat crash ID for new report                                                                 |
| crashType        | string       | String representing platform used to post report                                                 |
| stackKeyId       | number       | BugSplat ID for report group                                                                     |
| stackId          | number       | Unique call stack identifier                                                                     |
| stackKeyName     | string       | Function name and line number used to group the report                                           |
| callstack        | string array | Call stack.  This may be empty if BugSplat matches an existing crash using the crash hash field. |
| database         | string       | BugSplat database where report is stored                                                         |
| application      | string       | Application name associated with report                                                          |
| version          | string       | Versions associated with report                                                                  |
| key              | string       | Identifier for specific flavor of application associated with report                             |
| user             | string       | End-user associated with generated report                                                        |
| email            | string       | End-user's email associated with generated report                                                |
| ipAddress        | string       | End-user's IP address (can be obfuscated via Options page)                                       |
| hash             | string       | Hash representing unique report call stack.  Not available for all crashes                       |
| exceptionCode    | string       | Code associated with issue that caused the report to be generated                                |
| exceptionMessage | string       | Message associated with issue that caused the report to be generated                             |
| received         | datetime     | Time-stamp of generated report                                                                   |
