# Webhook

BugSplat’s Webhook integration allows your team to configure custom notifications for each new report or group of reports. BugSplat's Webhook integration allows you to build integrations unique and novel integrations that can be tailor-fit to your company's workflow.

## Configuring a BugSplat Webhook <a href="#integrating-slack-with-bugsplat-docs" id="integrating-slack-with-bugsplat-docs"></a>

1. Login to [BugSplat](https://app.bugsplat.com/cognito/login) and navigate to the [Notifications](https://app.bugsplat.com/v2/database/integrations#notifications) page.
2. Select the Database for which you'd like to configure alerts.
3. Under the **Webhook** section, enter the URL of the webhook you would like to invoke and click **Update**.
4. Use the toggle buttons to set your notification preferences. You can be notified for each new report or unique report group.

Once configured, BugSplat will post an object that matches the following interface to your Webhook. Unlike other notifications, there is no maximum throughput limitation.

## Webhook Notification

<mark style="color:green;">`POST`</mark> `https://your-domain.com/path/to/your/webhook`

Notification payload for new BugSplat report or new BugSplat group

#### Request Body

| Name             | Type     | Description                                                          |
| ---------------- | -------- | -------------------------------------------------------------------- |
| id               | number   | BugSplat ID for new report                                           |
| crashTypeId      | number   | Enum representing platform used to post report                       |
| parentStackKeyId | string   | BugSplat ID for report group before grouping rules were applied      |
| stackKeyId       | number   | BugSplat ID for report group                                         |
| newStackKeyId    | boolean  | True if this is the first occurence of stackKeyId                    |
| stackKeyName     | string   | Function name and line number used to group the report               |
| stackId          | number   | Unique call stack identifier                                         |
| newStackId       | boolean  | True if this is the first occurence of stackId                       |
| database         | string   | BugSplat database where report is stored                             |
| application      | string   | Application name associated with report                              |
| version          | string   | Versions associated with report                                      |
| key              | string   | Identifier for specific flavor of application associated with report |
| user             | string   | End-user associated with generated report                            |
| email            | string   | End-user's email associated with generated report                    |
| ipAddress        | string   | End-user's IP address (can be obfuscated via Options page)           |
| mfa              | string   | Hash representing report call stack                                  |
| exceptionCode    | string   | Code associated with issue that caused the report to be generated    |
| exceptionMessage | string   | Message associated with issue that caused the report to be generated |
| received         | datetime | Time-stamp of generated report                                       |
| reportXml        | string   | XML doc representing the fully processed BugSplat report             |
| debugOutput      | string   | Output generated when running the debugger                           |
