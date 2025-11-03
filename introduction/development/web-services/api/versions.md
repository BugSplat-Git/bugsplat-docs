---
description: API Documentation for the BugSplat Versions Endpoint
---

# Versions

{% hint style="info" %}
This endpoint supports paging, and filtering queries. More information on paging, filtering, and grouping is available [here](../paging-filtering-and-grouping.md).
{% endhint %}

Get a list stats for crashes that have been posted separated by application and version, create a new version, or set the retired and full dumps flags on a specified version.

## Versions

<mark style="color:blue;">`GET`</mark> `https://app.bugsplat.com/api/versions`

Returns a list of versions in a given database.

#### Query Parameters

| Name     | Type   | Description                                |
| -------- | ------ | ------------------------------------------ |
| database | string | BugSplat database containing symbol stores |

{% tabs %}
{% tab title="200 " %}
```json
{
  "database": "Fred",
  "pageData": [],
  "rows": [
    {
      "symbolId": "489912",
      "appName": "MyConsoleCrasher",
      "version": "7.0.1",
      "size": "46.1987",
      "lastUpdate": "2025-11-01T00:31:20Z",
      "lastReport": "2025-10-31T00:24:07Z",
      "firstReport": "2025-10-31T00:24:07Z",
      "reportsPerDay": null,
      "fullDumps": "0",
      "rejectedCount": "0",
      "retired": "0"
    }
  ]
}
```
{% endtab %}
{% endtabs %}

### Curl Example

```bash
curl --location 'https://app.bugsplat.com/api/versions?database=fred' \
--header 'Authorization: Bearer ••••••'
```

## Versions

<mark style="color:green;">`POST`</mark> `https://app.bugsplat.com/api/v2/versions`

Used to create a new version and returns a pre-signed URL that can be used to upload new symbol files.

#### Request Body

| Name                                         | Type   | Description                                                                                                                          |
| -------------------------------------------- | ------ | ------------------------------------------------------------------------------------------------------------------------------------ |
| database                                     | string | BugSplat database in which the symbol store should be created                                                                        |
| appVersion<mark style="color:red;">\*</mark> | string | Version of the application symbols being stored in the new symbol store                                                              |
| appName<mark style="color:red;">\*</mark>    | string | Name of application symbols being stored in the new symbol store                                                                     |
| symFileName                                  | string | Filename of the symbol file being uploaded to the symbol store via the returned pre-signed URL.                                      |
| size                                         | number | Size of symbol file being uploaded to the symbol store via the returned pre-signed URL. Enter 0 to create a placeholder symbol store |

{% tabs %}
{% tab title="200 " %}
```json
{
    "Status": "Success",
    "url": "",
    "database": "Fred",
    "appName": "test",
    "appVersion": "1.0"
}
```
{% endtab %}
{% endtabs %}

### Curl Example

```bash
curl --location 'https://app.bugsplat.com/api/versions' \
--header 'Authorization: Bearer ••••••' \
--form 'database="fred"' \
--form 'appName="Postman"' \
--form 'appVersion="1.2.3"' \
--form 'size="0"' \
--form 'symFileName="test.pdb"'
```

## Versions

<mark style="color:orange;">`PUT`</mark> `https://app.bugsplat.com/api/v2/versions`

Used to set the retired and fullDumps flags for a specified version.

#### Request Body

| Name                                         | Type   | Description                                                                                                                                                                                                                                                                                                                                                                           |
| -------------------------------------------- | ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| database                                     | string | BugSplat database in which the symbol store should be created                                                                                                                                                                                                                                                                                                                         |
| appVersion<mark style="color:red;">\*</mark> | string | Version of application for which the retired/fullDumps flags should be updated                                                                                                                                                                                                                                                                                                        |
| appName<mark style="color:red;">\*</mark>    | string | Name of application for which the retired/fullDumps flags should be updated                                                                                                                                                                                                                                                                                                           |
| fullDumps                                    | 0\|1   | <p>Flag indicating that the BugSplat Native and .NET SDKs should generate and upload</p><p><a href="../../../getting-started/integrations/desktop/cplusplus/full-memory-dumps.md">full memory dumps</a></p><p>. This feature incurs additional costs.</p><p><a href="../../../../administration/contact-us.md">Contact us</a></p><p>to enable full memory dumps for your account.</p> |
| retired                                      | 0\|1   | Flag indicating that the version should be marked as retired. Crash reports for retired versions will not be ingested or processed.                                                                                                                                                                                                                                                   |

{% tabs %}
{% tab title="200 " %}
```json
{
    "Status": "Success",
    "database": "Fred",
    "symbolId": 163434,
    "retired": 1
}
```
{% endtab %}
{% endtabs %}

## Versions

<mark style="color:red;">`DELETE`</mark> `https://app.bugsplat.com/api/v2/versions`

Remove symbols from a specified version or versions.

#### Path Parameters

| Name                                       | Type   | Description                                                                                                                                             |
| ------------------------------------------ | ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| database<mark style="color:red;">\*</mark> | string | BugSplat database containing versions with symbols for removal                                                                                          |
| appName                                    | string | Single application name to remove symbols for, required if appVersion is set                                                                            |
| appVersion                                 | string | Single application version to remove symbols for, required if appName is set                                                                            |
| appVersions                                | array  | Multi-dimensional array of appName and appVersion for symbol removal eg `app0,version0,app1,version1`.  Required if appName and appVersion are not set. |

### Curl Example

```bash
curl --location --request DELETE 'https://app.bugsplat.com/api/versions?database=fred&appName=Postman&appVersion=1.2.3' \
--header 'Authorization: Bearer ••••••'
```

