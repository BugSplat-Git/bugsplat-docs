---
description: API Documentation for the BugSplat Company Endpoint
---

# Company

Get or set information about your company in BugSplat for Billing purposes.

{% swagger baseUrl="https://app.bugsplat.com" path="/api/company" method="get" summary="Company" %}
{% swagger-description %}
Returns information about the queried company by companyId. 
{% endswagger-description %}

{% swagger-parameter in="query" name="companyId" type="number" required="true" %}
ID of the company to query
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
  companyId: 545,
  companyName: "BugSplat Public Testing",
  subscriptionId: 1732,
  birthdate: "0000-00-00 00:00:00",
  licensedLimitExceededCount: 0,
  licensedLimitExceededDate: null,
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger baseUrl="https://app.bugsplat.com" path="/api/company" method="put" summary="Company" %}
{% swagger-description %}
Set the name of the specified company by companyId.
{% endswagger-description %}

{% swagger-parameter in="query" name="companyId" type="number" required="true" %}
ID of the company to update
{% endswagger-parameter %}

{% swagger-parameter in="body" name="companyName" type="string" required="true" %}
Updated name for the specified company
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
  companyId: 545,
  companyName: "BugSplat Public Testing",
  subscriptionId: 1732,
  birthdate: "0000-00-00 00:00:00",
  licensedLimitExceededCount: 0,
  licensedLimitExceededDate: null,}
```
{% endswagger-response %}
{% endswagger %}
