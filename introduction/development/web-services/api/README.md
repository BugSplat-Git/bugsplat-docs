# Web Application Endpoints

BugSplat provides RESTful web services to access data on our backend. The BugSplat database should be selected using the "database" URL parameter. Data is returned in JSON format. Authentication is required for all endpoints. See the curl example at the end of this page for an illustration of this process.

Want to see our API in action? Open your web browser inspector to see how our web application uses the endpoints below!

Most API endpoints support a "database={name}" parameter to specify which BugSplat database to use. Without this parameter, the current (default) database will be selected.

{% content-ref url="charting.md" %}
[charting.md](charting.md)
{% endcontent-ref %}

{% content-ref url="company.md" %}
[company.md](company.md)
{% endcontent-ref %}

{% content-ref url="crash.md" %}
[crash.md](crash.md)
{% endcontent-ref %}

{% content-ref url="crashes.md" %}
[crashes.md](crashes.md)
{% endcontent-ref %}

{% content-ref url="crash-groups.md" %}
[crash-groups.md](crash-groups.md)
{% endcontent-ref %}

{% content-ref url="databases.md" %}
[databases.md](databases.md)
{% endcontent-ref %}

{% content-ref url="defect.md" %}
[defect.md](defect.md)
{% endcontent-ref %}

{% content-ref url="events.md" %}
[events.md](events.md)
{% endcontent-ref %}

{% content-ref url="support-response.md" %}
[support-response.md](support-response.md)
{% endcontent-ref %}

{% content-ref url="symbols.md" %}
[symbols.md](symbols.md)
{% endcontent-ref %}

{% content-ref url="user-gdpr.md" %}
[user-gdpr.md](user-gdpr.md)
{% endcontent-ref %}

{% content-ref url="users.md" %}
[users.md](users.md)
{% endcontent-ref %}

{% content-ref url="versions.md" %}
[versions.md](versions.md)
{% endcontent-ref %}

### Curl Example

You can access the web services with a variety of tools. The API authenticates with an [OAuth2](../oauth2.md) Client ID and Client Secret, which you can create on the [Integrations](https://app.bugsplat.com/v2/database/integrations#oauth) page. Here’s an example using Curl to exchange them for an access token and query the Fred database:

```bash
token=$(curl -s https://app.bugsplat.com/oauth2/authorize \
  -F grant_type=client_credentials \
  -F client_id="$BUGSPLAT_CLIENT_ID" \
  -F client_secret="$BUGSPLAT_CLIENT_SECRET" \
  | jq -r .access_token)

curl -H "Authorization: Bearer $token" "https://app.bugsplat.com/api/crashes?database=Fred"
curl -H "Authorization: Bearer $token" "https://app.bugsplat.com/api/crash/data?id=58464&database=Fred"
```

Access tokens expire after 24 hours. Request a new one with the same Client ID and Client Secret when yours expires.

### Postman Collection

Several of our API endpoints can be tested via our public [Postman collection](https://www.postman.com/bugsplat/bugsplat-public-workspace/overview).

First, click the top-level `BugSplat` collection folder, select the `Variables` tab, and input variables as necessary. Requests authenticate automatically when you provide values for `client_id` and `client_secret` generated on our [Integrations](https://app.bugsplat.com/v2/database/integrations#oauth) page.

## Special Rules for POST Requests

Requests authenticated with an OAuth2 bearer token don't need an XSRF token. The `Authorization` header is all a POST request needs.
