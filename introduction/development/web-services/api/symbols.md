---
description: API Documentation for the BugSplat Symbols Endpoint
---

# Symbols

{% hint style="info" %}
This endpoint supports paging and filtering queries. For more information on paging, filtering, and grouping, please visit this [link](../paging-filtering-and-grouping.md).
{% endhint %}

Get a list of the individual symbol files that have been uploaded to a database.

This endpoint returns one row per symbol file. To list symbol stores grouped by application and version, along with their crash counts and retired/full dump flags, use the [Versions](versions.md) endpoint instead.

## Get Symbols

<mark style="color:blue;">`GET`</mark> `https://app.bugsplat.com/api/v2/symbols`

Returns the symbol files uploaded to a given database. This query supports paging, filtering, and sorting. All of the property keys in the Rows object can be used as column values for filtering and sorting, e.g., application, version, moduleName, size, etc. Results are sorted by `lastModified` unless a `sortdatafield` is supplied.

#### Query Parameters

| Name     | Type   | Description                                                                             |
| -------- | ------ | ----------------------------------------------------------------------------------------- |
| database | string | BugSplat database containing the symbol files. Defaults to the current database if omitted. |

{% tabs %}
{% tab title="200 " %}
```json
{
  "Database": "fred",
  "PageData": null,
  "Rows": [
    {
      "application": "myConsoleCrasher",
      "version": "1.0.0",
      "s3Key": "Fred/myConsoleCrasher-1.0.0/myConsoleCrasher.pdb",
      "lastUploaded": "2025-11-01T00:31:20Z",
      "s3Bucket": "bugsplat-symbols",
      "symbolType": "Windows",
      "guid": "1254DF350E094F4582BB32676F926259",
      "size": "1047851",
      "lastModified": "2025-11-01T00:31:20Z",
      "lastAccessed": "2025-11-02T14:07:55Z",
      "moduleName": "myConsoleCrasher.pdb",
      "downloadFile": "https://app.bugsplat.com/symsrv/download?database=fred&key=Fred%2FmyConsoleCrasher-1.0.0%2FmyConsoleCrasher.pdb&file=myConsoleCrasher.pdb"
    }
  ]
}
```
{% endtab %}
{% endtabs %}

#### Response Fields

| Name         | Type   | Description                                                                                              |
| ------------ | ------ | ---------------------------------------------------------------------------------------------------------- |
| application  | string | Name of the application the symbol file belongs to.                                                      |
| version      | string | Version of the application the symbol file belongs to.                                                   |
| moduleName   | string | File name of the symbol file.                                                                            |
| s3Key        | string | Full key of the symbol file in BugSplat's symbol storage.                                                |
| s3Bucket     | string | Storage bucket holding the symbol file. May be null for older uploads.                                   |
| symbolType   | string | Type of symbol file, for example `Windows`. May be null or `Unknown` if the type could not be determined. |
| guid         | string | Debug identifier used to match the symbol file to a module in a crash report. May be null.               |
| size         | string | Size of the symbol file in bytes.                                                                        |
| lastUploaded | string | Date the symbol file was last uploaded. May be null for older uploads.                                   |
| lastModified | string | Date the symbol file record was last modified. May be null for older uploads.                             |
| lastAccessed | string | Date the symbol file was last used to symbolicate a crash. Used to determine [symbol expiry](../../working-with-symbol-files/managing-symbol-storage.md). May be null. |
| downloadFile | string | URL that can be used to download the symbol file.                                                        |

### Curl Example

```bash
curl --location 'https://app.bugsplat.com/api/v2/symbols?database=fred' \
--header 'Authorization: Bearer ••••••'
```

Filter to a single application and sort by the largest files first:

```bash
curl --location 'https://app.bugsplat.com/api/v2/symbols?database=fred&sortdatafield=size&sortorder=desc&filterscount=1&filterdatafield0=application&filtercondition0=EQUAL&filtervalue0=myConsoleCrasher' \
--header 'Authorization: Bearer ••••••'
```

{% hint style="info" %}
#### Deprecation Note

This endpoint was previously available at `https://app.bugsplat.com/api/symbolDetails`. That route still works but is deprecated and may be removed in the future. Use `https://app.bugsplat.com/api/v2/symbols` moving forward.
{% endhint %}
