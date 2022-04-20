---
description: API Documentation for the BugSplat Crashes Endpoint
---

# Import/Migrate

Endpoint to help your team to upload existing crashes and migrate from an existing crash report solution to BugSplat. This endpoint is not suitable for production crash volumes.

{% swagger method="get" path="/api/upload/manual/crash" baseUrl="https://app.bugsplat.com" summary="Crash Import" %}
{% swagger-description %}
Migrate crash minidump files to BugSplat from your existing crash report solution.  Note, this endpoint is not suiteable for production crash volumes
{% endswagger-description %}

{% swagger-parameter in="body" name="database" type="string" required="true" %}
Adds the crash to this BugSplat database
{% endswagger-parameter %}

{% swagger-parameter in="body" name="appName" type="string" required="true" %}
Application name that generated the corresponding minidump file. This must match the application name used to upload symbol files.
{% endswagger-parameter %}

{% swagger-parameter in="body" name="appVersion" type="string" required="true" %}
Application version that generated the corresponding minidump file. This must match the version used to upload symbol files.
{% endswagger-parameter %}

{% swagger-parameter in="body" name="minidump" type="file" required="true" %}
Minidump file to upload
{% endswagger-parameter %}

{% swagger-parameter in="body" name="crashTypeId" type="int" required="false" %}
TypeId for the corresponding minidump. 1=Windows Native, 6=Breakpad/Crashpad, 8=.NET Framework, 15=Unity Native Windows, 16=Unreal Engine (Linux Server), 17=Unreal Engine
{% endswagger-parameter %}

{% swagger-parameter in="body" name="appKey" type="string" %}
Optional appKey value
{% endswagger-parameter %}

{% swagger-parameter in="body" name="description" type="string" %}
Optional description value
{% endswagger-parameter %}

{% swagger-parameter in="body" name="email" type="string" %}
Optional email value
{% endswagger-parameter %}

{% swagger-parameter in="body" name="ipAddress" type="string" %}
Optional IP Address value
{% endswagger-parameter %}

{% swagger-parameter in="body" name="user" type="string" %}
Optional user value
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="" %}
```javascript
{
    status: "success",
    crashId: 1,
    techSupportUrl: "https://fred.bugsplat.com/browse/support/?stackKeyId=5555&vendor=Fred&key=*Default*"
}
```
{% endswagger-response %}
{% endswagger %}
