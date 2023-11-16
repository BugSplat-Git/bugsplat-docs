# Webhook

BugSplat’s Webhook integration allows your team to configure custom notifications for each new report or group of reports. BugSplat's Webhook integration allows you to build integrations unique and novel integrations that can be tailor-fit to your company's workflow.

## Configuring a BugSplat Webhook <a href="#integrating-slack-with-bugsplat-docs" id="integrating-slack-with-bugsplat-docs"></a>

1. Login to [BugSplat](https://app.bugsplat.com/cognito/login) and navigate to the [Notifications](https://app.bugsplat.com/v2/database/integrations#notifications) page.
2. Select the Database for which you'd like to configure alerts.
3. Under the **Webhook** section, enter the URL of the webhook you would like to invoke and click **Update**.
4. Use the toggle buttons to set your notification preferences. You can be notified for each new report or unique report group.

Once configured, BugSplat will post an object that matches the following interface to your Webhook with a maximum throughput of one notification per minute.

{% swagger method="post" path="your-domain.com/path/to/your/webhook" baseUrl="https://" summary="Webhook Notification" %}
{% swagger-description %}
Notification payload for new BugSplat report or new BugSplat group
{% endswagger-description %}

{% swagger-parameter in="body" name="id" type="number" required="false" %}
BugSplat ID for new report
{% endswagger-parameter %}

{% swagger-parameter in="body" name="crashTypeId" type="number" %}
Enum representing platform used to post report
{% endswagger-parameter %}

{% swagger-parameter in="body" name="parentStackKeyId" type="string" %}
BugSplat ID for report group before grouping rules were applied
{% endswagger-parameter %}

{% swagger-parameter in="body" name="stackKeyId" type="number" %}
BugSplat ID for report group
{% endswagger-parameter %}

{% swagger-parameter in="body" name="stackKeyName" type="string" %}
Function name and line number used to group the report
{% endswagger-parameter %}

{% swagger-parameter in="body" name="database" type="string" %}
BugSplat database where report is stored
{% endswagger-parameter %}

{% swagger-parameter in="body" name="application" type="string" %}
Application name associated with report
{% endswagger-parameter %}

{% swagger-parameter in="body" name="version" type="string" %}
Versions associated with report
{% endswagger-parameter %}

{% swagger-parameter in="body" name="key" type="string" %}
Identifier for specific flavor of application associated with report
{% endswagger-parameter %}

{% swagger-parameter in="body" name="user" type="string" %}
End-user associated with generated report
{% endswagger-parameter %}

{% swagger-parameter in="body" name="email" type="string " %}
End-user's email associated with generated report
{% endswagger-parameter %}

{% swagger-parameter in="body" name="ipAddress" type="string" %}
End-user's IP address (can be obfuscated via Options page)
{% endswagger-parameter %}

{% swagger-parameter in="body" name="mfa" type="string" %}
Hash representing report call stack
{% endswagger-parameter %}

{% swagger-parameter in="body" name="exceptionCode" type="string" %}
Code associated with issue that caused the report to be generated
{% endswagger-parameter %}

{% swagger-parameter in="body" name="exceptionMessage" type="string" %}
Message associated with issue that caused the report to be generated
{% endswagger-parameter %}

{% swagger-parameter in="body" name="received" type="datetime" %}
Time-stamp of generated report
{% endswagger-parameter %}

{% swagger-parameter in="body" name="reportXml" type="string" %}
XML doc representing the fully processed BugSplat report
{% endswagger-parameter %}

{% swagger-parameter in="body" name="debugOutput" type="string" %}
Output generated when running the debugger
{% endswagger-parameter %}
{% endswagger %}
