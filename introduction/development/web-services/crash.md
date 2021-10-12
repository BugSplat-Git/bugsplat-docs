# Crash

The following documentation describes how customers can POST crashes directly to BugSplat via a suite of endpoints specific to their BugSplat database. It is important that all these crashes are uploaded via your BugSplat subdomain to ensure that they are not rejected by our backend.

{% swagger baseUrl="https://{{database}}.bugsplat.com" path="/post/xbox/crash" method="post" summary="Xbox" %}
{% swagger-description %}
Uploads an Xbox crash report and optional metadata
{% endswagger-description %}

{% swagger-parameter in="path" name="{{database}}" type="string" %}
Replace the subdomain value  with the value of your BugSplat database
{% endswagger-parameter %}

{% swagger-parameter in="body" name="minidump" type="object" %}
The minidump file to be uploaded
{% endswagger-parameter %}

{% swagger-parameter in="body" name="appName" type="string" %}
Name of the crashing application. 

**IMPORTANT**

 this value must match the value used to upload symbols.
{% endswagger-parameter %}

{% swagger-parameter in="body" name="appVersion" type="string" %}
Crashing application's version. 

**IMPORTANT**

 this value must match the value used to upload symbols
{% endswagger-parameter %}

{% swagger-parameter in="body" name="appKey" type="string" %}
Optional application identifier that provides extra data for searching and grouping
{% endswagger-parameter %}

{% swagger-parameter in="body" name="description" type="string" %}
Optional description of why the crash occurred
{% endswagger-parameter %}

{% swagger-parameter in="body" name="email" type="string" %}
Optional email address for the user that crashed
{% endswagger-parameter %}

{% swagger-parameter in="body" name="ipAddress" type="string" %}
Optional IP address of the crashing user
{% endswagger-parameter %}

{% swagger-parameter in="body" name="user" type="string" %}
Optional username for the user that crashed
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
    "status": "success",
    "crashId": 1,
    "techSupportUrl": "https://app.bugsplat.com/browse/crashInfo.php?vendor=fred&version=1.0&key=key&id=99999999&row=1"
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger baseUrl="https://{{database}}.bugsplat.com" path="/post/playstation/crash" method="post" summary="Playstation" %}
{% swagger-description %}
Uploads a Playstation crash report and optional metadata
{% endswagger-description %}

{% swagger-parameter in="path" name="{{database}}" type="string" %}
Replace the subdomain value  with the value of your BugSplat database
{% endswagger-parameter %}

{% swagger-parameter in="body" name="core" type="object" %}
The core dump file to be uploaded
{% endswagger-parameter %}

{% swagger-parameter in="body" name="appName" type="string" %}
Name of the crashing application. 

**IMPORTANT**

 this value must match the value used to upload symbols.
{% endswagger-parameter %}

{% swagger-parameter in="body" name="appVersion" type="string" %}
Crashing application's version. 

**IMPORTANT**

 this value must match the value used to upload symbols
{% endswagger-parameter %}

{% swagger-parameter in="body" name="appKey" type="string" %}
Optional application identifier that provides extra data for searching and grouping
{% endswagger-parameter %}

{% swagger-parameter in="body" name="description" type="string" %}
Optional description of why the crash occurred
{% endswagger-parameter %}

{% swagger-parameter in="body" name="email" type="string" %}
Optional email address for the user that crashed
{% endswagger-parameter %}

{% swagger-parameter in="body" name="ipAddress" type="string" %}
Optional IP address of the crashing user
{% endswagger-parameter %}

{% swagger-parameter in="body" name="user" type="string" %}
Optional username for the user that crashed
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
    "status": "success",
    "crashId": 1,
    "techSupportUrl": "https://app.bugsplat.com/browse/crashInfo.php?vendor=fred&version=1.0&key=key&id=99999999&row=1"
}
```
{% endswagger-response %}
{% endswagger %}

### Example

The following is an example that uses curl to demonstrate posting a crash to BugSplat. Be sure to update the value of `{{database}}` with the value of your BugSplat database.

```
curl --request POST 'https://{{database}}.bugsplat.com/post/xbox/crash' \
--form 'appName="my-xbox-crasher"' \
--form 'appVersion="1.0.0"' \
--form 'minidump=@"/path/to/minidump.dmp"'
```
