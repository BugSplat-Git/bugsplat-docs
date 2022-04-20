# Web Application Endpoints

BugSplat provides RESTful web services to access data on our backend. The BugSplat database should be selected using the "database" URL parameter. Data is returned in JSON format. Authentication is required for all endpoints. See the curl example at the end of this page for an illustration of this process.

Want to see our API in action? Open your web browser inspector to see how our web application uses the endpoints below!

Most API endpoints support a "database={name}" parameter, used to specify which BugSplat database to use. In the absence of this parameter, the current (default) database will be selected.

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

{% content-ref url="user-gdpr.md" %}
[user-gdpr.md](user-gdpr.md)
{% endcontent-ref %}

{% content-ref url="users.md" %}
[users.md](users.md)
{% endcontent-ref %}

{% content-ref url="versions.md" %}
[versions.md](versions.md)
{% endcontent-ref %}

### Example

You can access the web services with a variety of tools. Here’s an example using Curl to connect to the Fred database:

```
rm cookies.txt
curl -b cookies.txt -c cookies.txt --data "email=fred@bugsplat.com&password=Flintstone" https://app.bugsplat.com/api/authenticatev3
curl -b cookies.txt -c cookies.txt "https://app.bugsplat.com/allCrash?database=Fred"
curl -b cookies.txt -c cookies.txt "https://app.bugsplat.com/api/crash/data?id=58464&database=Fred"
```

## Special Rules for POST Requests

When POSTing data to BugSplat endpoints additional steps are required to meet our Cross Site Request Forgery (XSRF) safety checks. After authenticating you will receive a cookie named `xsrf-token`. Send the xsrf-token key/value as a **header** in all POST requests.
