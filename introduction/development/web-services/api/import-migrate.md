---
description: API Documentation for the BugSplat Crashes Endpoint
---

# Import/Migrate

Endpoint to help your team to upload existing crashes and migrate from an existing crash report solution to BugSplat. This endpoint is not suitable for production crash volumes.

## Crash Import

<mark style="color:green;">`POST`</mark> `https://{database}.bugsplat.com/api/upload/manual/crash`

Migrate crash minidump files to BugSplat from your existing crash report solution. Note, this endpoint is not suitable for production crash volumes.  Replace {database} with the name of your BugSplat database.

#### Request Body

| Name                                         | Type   | Description                                                                                                                                                                |
| -------------------------------------------- | ------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| appName<mark style="color:red;">\*</mark>    | string | Application name that generated the corresponding minidump file. This must match the application name used to upload symbol files.                                         |
| appVersion<mark style="color:red;">\*</mark> | string | Application version that generated the corresponding minidump file. This must match the version used to upload symbol files.                                               |
| minidump<mark style="color:red;">\*</mark>   | file   | Minidump file to upload                                                                                                                                                    |
| crashTypeId                                  | int    | TypeId for the corresponding minidump. 1=Windows Native, 6=Breakpad/Crashpad, 8=.NET Framework, 15=Unity Native Windows, 16=Unreal Engine (Linux Server), 17=Unreal Engine |
| appKey                                       | string | Optional appKey value                                                                                                                                                      |
| description                                  | string | Optional description value                                                                                                                                                 |
| email                                        | string | Optional email value                                                                                                                                                       |
| ipAddress                                    | string | Optional IP Address value                                                                                                                                                  |
| user                                         | string | Optional user value                                                                                                                                                        |

{% tabs %}
{% tab title="200: OK " %}
```javascript
{
    status: "success",
    crashId: 1,
    techSupportUrl: "https://fred.bugsplat.com/browse/support/?stackKeyId=5555&vendor=Fred&key=*Default*"
}
```
{% endtab %}
{% endtabs %}
