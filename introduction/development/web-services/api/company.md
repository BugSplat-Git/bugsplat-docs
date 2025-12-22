---
description: API Documentation for the BugSplat Company Endpoint
---

# Company

Get or set information about your company in BugSplat for Billing purposes.

## Get Company

<mark style="color:blue;">`GET`</mark> `https://app.bugsplat.com/api/company`

Returns information about the queried company by companyId.

#### Query Parameters

| Name                                        | Type   | Description                |
| ------------------------------------------- | ------ | -------------------------- |
| companyId<mark style="color:red;">\*</mark> | number | ID of the company to query |

{% tabs %}
{% tab title="200 " %}
```json
{
  companyId: 545,
  companyName: "BugSplat Public Testing",
  subscriptionId: 1732,
  birthdate: "0000-00-00 00:00:00",
  licensedLimitExceededCount: 0,
  licensedLimitExceededDate: null,
}
```
{% endtab %}
{% endtabs %}

### Curl Example

```bash
curl --location 'https://app.bugsplat.com/api/company?database=fred' \
--header 'Cookie: user=fred%40bugsplat.com; PHPSESSID=4g3pr9rdtehoddac9ohrt1qrc3cehg54; xsrf-token=ajKqRF12QJJ3IlLFdCEH0RhMjpuD5scz86mul3zdjTYA'
```

## Update Company

<mark style="color:orange;">`PUT`</mark> `https://app.bugsplat.com/api/company`

Set the name of the specified company by companyId.

#### Query Parameters

| Name                                        | Type   | Description                 |
| ------------------------------------------- | ------ | --------------------------- |
| companyId<mark style="color:red;">\*</mark> | number | ID of the company to update |

#### Request Body

| Name                                          | Type   | Description                            |
| --------------------------------------------- | ------ | -------------------------------------- |
| companyName<mark style="color:red;">\*</mark> | string | Updated name for the specified company |

{% tabs %}
{% tab title="200 " %}
```json
{
  companyId: 545,
  companyName: "BugSplat Public Testing",
  subscriptionId: 1732,
  birthdate: "0000-00-00 00:00:00",
  licensedLimitExceededCount: 0,
  licensedLimitExceededDate: null
}
```
{% endtab %}
{% endtabs %}
