---
description: >-
  What a coding agent needs to add BugSplat to an app: the values to ask the
  developer for, SDK downloads that don't need a login, symbol upload, how to
  confirm a test crash arrived, and reading crashes through the MCP server.
---

# For AI Coding Agents

You can hand a BugSplat integration to a coding agent such as Claude Code, Cursor or GitHub Copilot. This page lists what the agent needs from you, where it can download each SDK without signing in, and how it can confirm that a test crash reached your database.

If you are an agent, work through the sections in order, and ask the developer for everything in the first section before you change any code.

## Reading these docs

Every page is available as Markdown: add `.md` to its URL, for example `https://docs.bugsplat.com/sdks/desktop/windows.md`. [llms.txt](https://docs.bugsplat.com/llms.txt) lists every page with a one-line description.

## Values to get from the developer

| Value | Where the developer finds it | Environment variable |
| --- | --- | --- |
| Database name | The [Database](https://app.bugsplat.com/v2/database) page | `BUGSPLAT_DATABASE` |
| Application name and version | Their product. The app and the symbol upload must use the same values. | |
| OAuth Client ID and Client Secret | The OAuth tab of the [Integrations](https://app.bugsplat.com/v2/database/integrations#oauth) page | `SYMBOL_UPLOAD_CLIENT_ID`, `SYMBOL_UPLOAD_CLIENT_SECRET` |

The developer creates the database and the OAuth client in the web app. Ask them to do it; don't sign in with their email address and password to do it for them.

Ask for a client with the **restricted** scope. It can upload symbols and read crashes from its database, which covers every step on this page. A client with the **symbols** scope can only upload symbols. See [OAuth2](../development/web-services/oauth2.md).

Keep the Client Secret out of the repository. Read it from the environment, or from a file the repository ignores.

## Downloading the SDK

Most SDKs install from a package manager; the platform's page names the package. The Windows SDKs are also zip files you can download without a login:

| SDK | Download |
| --- | --- |
| [BugSplat for Windows (C++)](../../sdks/desktop/windows/) | `https://app.bugsplat.com/download/bugsplat-windows.zip`, or in a `.vcxproj`, the [BugSplat](https://www.nuget.org/packages/BugSplat) NuGet package, which gives the project the C API (see [NuGet](../../sdks/desktop/windows/#nuget)) |
| [BugSplat for .NET](../../sdks/desktop/bugsplat-for-dot-net.md) | The [BugSplat](https://www.nuget.org/packages/BugSplat) NuGet package, or `https://app.bugsplat.com/download/bugsplat-dotnet.zip` |

Each zip URL returns the latest release. Releases after 9.2.0 can also be pinned: add `?version=` and the release's version number. A version that isn't available returns a 404.

```powershell
Invoke-WebRequest -Uri "https://app.bugsplat.com/download/bugsplat-windows.zip" -OutFile BugSplat.zip
Expand-Archive BugSplat.zip -DestinationPath "$env:TEMP\BugSplatSdk"
Copy-Item -Recurse "$env:TEMP\BugSplatSdk\BugSplat" third_party\BugSplat
```

The zip also contains the sample applications. The `BugSplat` folder is the only part an app needs.

To find which release a repository already has, read `BUGSPLAT_VERSION` in `BugSplat\inc\BugSplatVersion.h`. Releases before 9.3.0 don't have that file.

## Uploading symbols

Upload the symbol files from every build you run or ship, using the same application name and version the app reports. With the environment variables above set, the `symbol-upload` tool needs only the application, version and files:

```bash
npx @bugsplat/symbol-upload -a MyApp -v 1.0.0 -d build/Release -f "*.{pdb,exe,dll}"
```

`-f` defaults to `*.js.map`, so a native build has to pass its own pattern. Without Node.js, use the standalone binary, for example `https://app.bugsplat.com/download/symbol-upload-windows.exe`. See [Upload Symbols with symbol-upload](../development/working-with-symbol-files/upload-symbols-with-symbol-upload.md) for the full list of options.

The [BugSplat CLI](../development/web-services/bugsplat-cli.md) uploads symbols too. `bugsplat symbols upload` takes the same `-a`, `-v`, `-d`, and `-f` options and reads the same environment variables, and `bugsplat symbols upload --dry-run` lists what a pattern selects without uploading anything.

## Confirming a crash arrived

1. Make the app crash. Each platform's page ends with a verification step that shows how. On Windows, run the app without a debugger attached, and call `SetQuietMode(true)` for the test so the report uploads without waiting for someone to answer the crash dialog. Turn quiet mode back off before you ship, unless the developer wants no dialog.
2. Exchange the OAuth client for a token, then list the database's crashes:

```powershell
$token = (Invoke-RestMethod -Method Post -Uri "https://app.bugsplat.com/oauth2/authorize" -Body @{
    grant_type    = "client_credentials"
    client_id     = $env:SYMBOL_UPLOAD_CLIENT_ID
    client_secret = $env:SYMBOL_UPLOAD_CLIENT_SECRET
}).access_token

Invoke-RestMethod -Uri "https://app.bugsplat.com/api/crashes?database=$env:BUGSPLAT_DATABASE" -Headers @{
    Authorization = "Bearer $token"
}
```

3. Find the row whose `appName`, `appVersion` and `crashTime` match the test. If its `stackKey` is a module and an offset, such as `MyApp+0x12b5`, the symbols for that version weren't uploaded or don't match the build. The [Crashes](../development/web-services/api/crashes.md) API page shows how to filter the list.

If no row appears, each platform's page lists the usual causes. For Windows C++, see [When crashes don't arrive](../../sdks/desktop/windows/#when-crashes-dont-arrive).

## Reading crashes through the MCP server

Once crashes are arriving, the agent can work with them through BugSplat's hosted [MCP server](../development/web-services/mcp-server/) at `https://api.bugsplat.com/mcp`. The developer adds that address to their agent and signs in with their own BugSplat account in the browser, so ask them to connect it. The agent then has the same access as the developer's login, in every database they can open, with no Client ID or Client Secret to manage.

The server's tools list and read crash reports, crash groups, versions and symbol files, file a crash group as a defect in the database's connected issue tracker, and mark a group fixed. To confirm a test crash with it, ask for the latest reports in the database and match the application, version and crash time as in step 3 above. [MCP Server](../development/web-services/mcp-server/) has the setup steps for each client, and [MCP Tools](../development/web-services/mcp-server/tools.md) lists every tool and its arguments.

An agent that can't connect to an MCP server can run the [BugSplat CLI](../development/web-services/bugsplat-cli.md), whose commands call the same tools: `bugsplat reports list` lists the latest reports, and `bugsplat reports get <id>` shows one in full. Where no browser can open, the CLI signs in with the Client ID and Client Secret from the environment variables above.
