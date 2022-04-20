---
description: API Documentation for the BugSplat Support Response Endpoints
---

# Support Response

Get or set the contents of the end-user [Support Response page](../../../production/setting-up-custom-support-responses.md) that is loaded by the BugSplat [Windows Native C++](../../../getting-started/integrations/desktop/cplusplus/) and [Windows .NET Framework](../../../getting-started/integrations/desktop/windows-dot-net-framework.md) SDKs.

{% swagger baseUrl="https://app.bugsplat.com" path="/api/techsupport/message" method="get" summary="Message" %}
{% swagger-description %}
Returns the subject and raw markdown body contents for a Support Response page that has been configured for a specified stackKeyId and appKey.
{% endswagger-description %}

{% swagger-parameter in="query" name="appKey" type="string" %}
An appKey used to display a targeted version of the Support Response page. This is useful for displaying localized Support Response pages.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="stackKeyId" type="number" required="true" %}
The Crash Group (Stack Key) ID of the specified crash
{% endswagger-parameter %}

{% swagger-parameter in="query" name="database" type="string" %}
BugSplat database containing the specified crash
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
    "status": "success",
    "stackKeyId": 5524,
    "stackKey": "myConsoleCrasher!MemoryException(153)",
    "appKey": "*Default*",
    "subject": "",
    "content": "",
    "allkeys": null
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger baseUrl="https://app.bugsplat.com" path="/api/techsupport/message" method="post" summary="Message" %}
{% swagger-description %}
Create a new Support Response page for a specified stackKeyId and appKey.
{% endswagger-description %}

{% swagger-parameter in="query" name="content" type="string" required="true" %}
Markdown contents of the Support Response page's body.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="subject" type="string" required="true" %}
The subject line to be displayed to an end-user.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="appKey" type="string" %}
An appKey used to display a targeted version of the Support Response page. This is useful for displaying localized Support Response pages.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="stackKeyId" type="number" required="true" %}
The Crash Group (Stack Key) ID of the specified crash
{% endswagger-parameter %}

{% swagger-parameter in="query" name="database" type="string" %}
BugSplat database containing the specified crash
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
    "status": "success",
    "stackKeyId": 0,
    "appKey": "*Default*",
    "subject": "Default Message",
    "content": "This is the default support response for crashes posted to the Fred database.  It will be displayed if there is no stack key specific support response available."
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger baseUrl="https://app.bugsplat.com" path="/api/techsupport/message" method="put" summary="Message" %}
{% swagger-description %}
Update a Support Response page for a specified stackKeyId and appKey.
{% endswagger-description %}

{% swagger-parameter in="query" name="content" type="string" required="true" %}
Markdown contents of the Support Response page's body.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="subject" type="string" required="true" %}
The subject line to be displayed to an end-user.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="appKey" type="string" %}
An appKey used to display a targeted version of the Support Response page. This is useful for displaying localized Support Response pages.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="stackKeyId" type="number" required="true" %}
The Crash Group (Stack Key) ID of the specified crash
{% endswagger-parameter %}

{% swagger-parameter in="query" name="database" type="string" %}
BugSplat database containing the specified crash
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
    "status": "success",
    "stackKeyId": 0,
    "appKey": "*Default*",
    "subject": "Default Message",
    "content": "This is the default support response for crashes posted to the Fred database.  It will be displayed if there is no stack key specific support response available."}
```
{% endswagger-response %}
{% endswagger %}

{% swagger baseUrl="https://app.bugsplat.com" path="/api/techsupport/message" method="delete" summary="Message" %}
{% swagger-description %}
Delete a Support Response page for a specified stackKeyId and appKey.
{% endswagger-description %}

{% swagger-parameter in="query" name="appKey" type="string" %}
An appKey used to display a targeted version of the Support Response page. This is useful for displaying localized Support Response pages.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="stackKeyId" type="number" required="true" %}
The Crash Group (Stack Key) ID of the specified crash
{% endswagger-parameter %}

{% swagger-parameter in="query" name="database" type="string" %}
BugSplat database containing the specified crash
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
    "status": "success",
    "stackKeyId": 5524,
    "appKey": "en-US",
    "affected_rows": 1
}
```
{% endswagger-response %}
{% endswagger %}
