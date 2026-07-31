# What IP Addresses Does BugSplat Use?

Some organizations restrict access to their symbol servers, defect trackers, and other internal services by IP address. To allow access to BugSplat, add the following IP addresses to your allowlist:

* `23.22.79.2`
* `3.93.104.250`
* `34.194.164.107`

Allowlisting these addresses ensures BugSplat can connect to:

* [Symbol servers](../../introduction/development/working-with-symbol-files/symbol-servers.md) — so BugSplat can fetch symbols when processing your crash reports
* [Defect tracker integrations](../../introduction/development/integrating-with-tools/issue-trackers/README.md) such as Jira, YouTrack, Azure DevOps, GitHub Issues, and GitLab — so BugSplat can create and update issues on your behalf

If you're still having trouble connecting BugSplat to your services after allowlisting these addresses, please send us a note at [support@bugsplat.com](mailto:support@bugsplat.com).
