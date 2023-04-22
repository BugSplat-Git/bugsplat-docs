# Android

{% swagger method="post" path="/post/android/symbols" baseUrl="https://{{database}}.bugsplat.com" summary="Symbols" expanded="true" %}
{% swagger-description %}
Transforms 

`.so`

 files to 

`.sym`

 files
{% endswagger-description %}

{% swagger-parameter in="path" name="{{database}}" required="true" %}
Replace the subdomain value with the value of your BugSplat database
{% endswagger-parameter %}

{% swagger-parameter in="body" required="true" name="database" %}
The BugSplat database to upload to
{% endswagger-parameter %}

{% swagger-parameter in="body" name="appName" required="true" %}
The name of your application. 

**IMPORTANT**

 this value must match the value used to configure the crash reporter.
{% endswagger-parameter %}

{% swagger-parameter in="body" name="appVersion" required="true" %}
The version of your application. 

**IMPORTANT**

 this value must match the value used to configure the crash reporter.
{% endswagger-parameter %}

{% swagger-parameter in="body" name="file" type="File" required="true" %}
The 

`.so`

 file that BugSplat should run 

[dump-syms](../../../getting-started/integrations/cross-platform/crashpad/how-to-build-google-crashpad.md#linux-2)

 on to generate a 

`.sym`

 file.
{% endswagger-parameter %}

{% swagger-response status="202: Accepted" description="The `.so` file has been accepted for processing" %}
```json
{
    message: "File my-ubuntu-crasher upload accepted. The file will be available after it has been processed.",
    url: "https://app.bugsplat.com/v2/versions/symbols?appName=my-ubuntu-crasher&version=1.0-test&database=fred&c0=module&f0=CONTAINS&v0=my-ubuntu-crasher"
}
```
{% endswagger-response %}
{% endswagger %}
