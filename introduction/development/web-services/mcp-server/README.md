---
description: >-
  Connect BugSplat to Claude Code, Claude, ChatGPT, Codex, and more. BugSplat's
  hosted MCP server can query all your crash data via plain-english without
  package installs or credential management.
---

# MCP Server

Ask your AI assistant which crash groups showed up in your latest build, and it answers from your BugSplat databases. BugSplat hosts a Model Context Protocol (MCP) server at this address:

```
https://api.bugsplat.com/mcp
```

Add the address to Claude, Claude Code, Cursor, Codex, ChatGPT, or any other MCP client that connects to remote servers and signs in with OAuth.

The first time your assistant connects, your browser opens BugSplat's sign-in page. After you sign in, the server works with your own BugSplat permissions: the assistant can read every database your login can open. You don't create a Client ID and Client Secret for each database the way you do for the [REST API](../oauth2.md), where each pair belongs to one database.

The server's tools list and read crash reports, crash groups, versions, and symbol files, three tools file a crash group as a defect in your connected issue tracker or manage that link, and one marks a crash group fixed or sets its other statuses. [MCP Tools](tools.md) lists each tool's arguments, the filter syntax, and example questions.

{% hint style="info" %}
API and MCP access is included on every plan. On the Free plan, access is rate limited. See [Plans and Billing Overview](../../../../administration/billing/plans-and-billing-guide.md).
{% endhint %}

## Connect your assistant

Each page has the setup steps for one client:

| Client | Setup |
| --- | --- |
| Claude and Claude Desktop | [Connect Claude to BugSplat](claude.md) |
| Claude Code | [Connect Claude Code to BugSplat](claude-code.md) |
| Cursor | [Connect Cursor to BugSplat](cursor.md) |
| Codex | [Connect Codex to BugSplat](codex.md) |
| ChatGPT | [Connect ChatGPT to BugSplat](chatgpt.md) |

## The bugsplat-mcp package

The [bugsplat-mcp](https://github.com/BugSplat-Git/bugsplat-mcp) package is an MCP server that runs on your own computer. Each configuration reads one database with that database's [Client ID and Client Secret](../oauth2.md). Its README covers setup.
