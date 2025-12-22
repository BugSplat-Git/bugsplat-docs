---
description: API Documentation for the BugSplat Databases Endpoint
---

# Databases

Operations related to a user or company's databases in BugSplat. Get a list of databases for the currently authenticated user or company, create a new database, transfer a database's ownership to another company, or delete a database.

## Get Databases

<mark style="color:blue;">`GET`</mark> `https://app.bugsplat.com/api/databases`

Returns databases for the current user or a company specified by companyId.

#### Query Parameters

| Name      | Type   | Description                               |
| --------- | ------ | ----------------------------------------- |
| companyId | number | ID of the company that owns the databases |

{% tabs %}
{% tab title="200 " %}
```json
[{
    "dbName": "Fred",
    "companyId": "545",
    "companyName": "BugSplat Public Testing",
    "Volume30Day": "1075",
    "Volume365Day": "8868",
    "CrashDataDays": "6544"
}]
```
{% endtab %}
{% endtabs %}

### Curl Example

```bash
curl --location 'https://app.bugsplat.com/api/databases' \
--header 'Cookie: user=fred%40bugsplat.com; PHPSESSID=4g3pr9rdtehoddac9ohrt1qrc3cehg54; xsrf-token=ajKqRF12QJJ3IlLFdCEH0RhMjpuD5scz86mul3zdjTYA'
```

## Create Database

<mark style="color:green;">`POST`</mark> `https://app.bugsplat.com/api/databases`

Create a new database for the current user. Default values for the new database are copied from the current database.

#### Request Body

| Name                                       | Type   | Description                        |
| ------------------------------------------ | ------ | ---------------------------------- |
| database<mark style="color:red;">\*</mark> | string | Name of the database being created |

{% tabs %}
{% tab title="200 " %}
```json
{
    "status": "success",
    "database": "fred"
}
```
{% endtab %}
{% endtabs %}

### Curl Example

```bash
curl --location 'https://app.bugsplat.com/api/databases' \
--header 'xsrf-token: ajKqRF12QJJ3IlLFdCEH0RhMjpuD5scz86mul3zdjTYA' \
--header 'Cookie: user=fred%40bugsplat.com; PHPSESSID=4g3pr9rdtehoddac9ohrt1qrc3cehg54; xsrf-token=ajKqRF12QJJ3IlLFdCEH0RhMjpuD5scz86mul3zdjTYA' \
--form 'database="postman-4f17b3f4-7ff0-497c-b3ec-94b333c05bd6"'
```

## Update Databases

<mark style="color:orange;">`PUT`</mark> `https://app.bugsplat.com/api/databases`

Transfer ownership of a database to another company.

#### Request Body

| Name      | Type   | Description                                            |
| --------- | ------ | ------------------------------------------------------ |
| companyId | number | ID of the company the database is being transferred to |
| database  | string | Name of the database being transferred                 |

{% tabs %}
{% tab title="200 " %}
```json
{
    "status": "success",
    "database": "fred",
    "companyId": 25
}
```
{% endtab %}
{% endtabs %}

## Delete Database

<mark style="color:red;">`DELETE`</mark> `https://app.bugsplat.com/api/databases`

Deletes a database and all associated data from BugSplat. This action is irreversible

#### Query Parameters

| Name     | Type   | Description                 |
| -------- | ------ | --------------------------- |
| database | string | BugSplat database to delete |

{% tabs %}
{% tab title="200 " %}
```
{
    "status": "success",
    "database": "fred",
    "deleted": true
}
```
{% endtab %}
{% endtabs %}
