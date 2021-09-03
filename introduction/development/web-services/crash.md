# Crash

The following documentation describes how customers can POST crashes directly to BugSplat via a suite of endpoints specific to their BugSplat database. It is important that all these crashes are uploaded via your BugSplat subdomain to ensure that they are not rejected by our backend.

{% api-method method="post" host="https://{{database}}.bugsplat.com" path="/post/xbox/crash" %}
{% api-method-summary %}
Xbox
{% endapi-method-summary %}

{% api-method-description %}
Uploads an Xbox crash report and optional metadata
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="{{database}}" type="string" required=true %}
Replace the subdomain value  with the value of your BugSplat database
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}

{% api-method-form-data-parameters %}
{% api-method-parameter name="minidump" type="object" required=true %}
The minidump file to be uploaded
{% endapi-method-parameter %}

{% api-method-parameter name="appName" type="string" required=true %}
Name of the crashing application. **IMPORTANT** this value must match the value used to upload symbols.
{% endapi-method-parameter %}

{% api-method-parameter name="appVersion" type="string" required=true %}
Crashing application's version. **IMPORTANT** this value must match the value used to upload symbols
{% endapi-method-parameter %}

{% api-method-parameter name="appKey" type="string" required=false %}
Optional application identifier that provides extra data for searching and grouping
{% endapi-method-parameter %}

{% api-method-parameter name="description" type="string" required=false %}
Optional description of why the crash occurred
{% endapi-method-parameter %}

{% api-method-parameter name="email" type="string" required=false %}
Optional email address for the user that crashed
{% endapi-method-parameter %}

{% api-method-parameter name="ipAddress" type="string" required=false %}
Optional IP address of the crashing user
{% endapi-method-parameter %}

{% api-method-parameter name="user" type="string" required=false %}
Optional username for the user that crashed
{% endapi-method-parameter %}
{% endapi-method-form-data-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```text
{
    "status": "success",
    "crashId": 1,
    "techSupportUrl": "https://app.bugsplat.com/browse/crashInfo.php?vendor=fred&version=1.0&key=key&id=99999999&row=1"
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="post" host="https://{{database}}.bugsplat.com" path="/post/playstation/crash" %}
{% api-method-summary %}
Playstation
{% endapi-method-summary %}

{% api-method-description %}
Uploads a Playstation crash report and optional metadata
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="{{database}}" type="string" required=true %}
Replace the subdomain value  with the value of your BugSplat database
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}

{% api-method-form-data-parameters %}
{% api-method-parameter name="core" type="object" required=true %}
The core dump file to be uploaded
{% endapi-method-parameter %}

{% api-method-parameter name="appName" type="string" required=true %}
Name of the crashing application. **IMPORTANT** this value must match the value used to upload symbols.
{% endapi-method-parameter %}

{% api-method-parameter name="appVersion" type="string" required=true %}
Crashing application's version. **IMPORTANT** this value must match the value used to upload symbols
{% endapi-method-parameter %}

{% api-method-parameter name="appKey" type="string" required=false %}
Optional application identifier that provides extra data for searching and grouping
{% endapi-method-parameter %}

{% api-method-parameter name="description" type="string" required=false %}
Optional description of why the crash occurred
{% endapi-method-parameter %}

{% api-method-parameter name="email" type="string" required=false %}
Optional email address for the user that crashed
{% endapi-method-parameter %}

{% api-method-parameter name="ipAddress" type="string" required=false %}
Optional IP address of the crashing user
{% endapi-method-parameter %}

{% api-method-parameter name="user" type="string" required=false %}
Optional username for the user that crashed
{% endapi-method-parameter %}
{% endapi-method-form-data-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```text
{
    "status": "success",
    "crashId": 1,
    "techSupportUrl": "https://app.bugsplat.com/browse/crashInfo.php?vendor=fred&version=1.0&key=key&id=99999999&row=1"
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

