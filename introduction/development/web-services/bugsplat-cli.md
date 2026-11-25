---
description: >-
  Install the BugSplat CLI, sign in, read crash reports and crash groups, and
  inspect, find, upload, and delete symbol files from a terminal, a build
  script, or a coding agent.
---

# BugSplat CLI

List this week's crash groups, read a crash report's call stack, and upload a build's symbol files from one command: `bugsplat`. The BugSplat CLI is for people at a terminal, for build scripts, and for coding agents:

* At a terminal, you sign in once in your browser and work in every database your BugSplat login can open.
* A build script or CI job uploads symbol files with a Client ID and Client Secret it reads from environment variables.
* A coding agent that can run shell commands gets the same crash data the [MCP server](mcp-server/)'s tools return. An agent that runs where no browser can open, such as a container, signs in the way a CI job does.

## Install

```bash
npm install -g @bugsplat/cli
```

The package installs the `bugsplat` command on macOS, Linux, and Windows, on x64 and arm64. You need Node.js 14.18 or later to install the package and to run `bugsplat`, because the command starts the binary for your platform through Node.js. On a machine without Node.js, download the binary for your platform from the [releases page](https://github.com/BugSplat-Git/bugsplat-cli/releases). The binary runs without Node.js.

`bugsplat --version` prints the version, and `bugsplat --help` lists the commands. Every command has its own `--help`, for example `bugsplat reports list --help`.

## Sign in

```bash
bugsplat login
```

`bugsplat login` opens your browser so you can sign in to BugSplat. With `--no-browser`, it prints the sign-in URL and leaves opening it to you. `bugsplat whoami` shows who is signed in, and `bugsplat logout` deletes the stored session.

The CLI stores your session in your operating system's keychain. On a machine without a keychain, such as a container, it stores the session in a file that only your user can read. The file is in the `bugsplat` folder of your user configuration directory: `~/.config/bugsplat` on Linux, `~/Library/Application Support/bugsplat` on macOS, and `%APPDATA%\bugsplat` on Windows. Two environment variables change where the session goes:

| Environment variable | Value |
| --- | --- |
| `BUGSPLAT_TOKEN_STORE` | `file` stores the session in the file. `keychain` stores it in the keychain. `auto`, the default, uses the keychain when the machine has one and the file when it doesn't. |
| `BUGSPLAT_CONFIG_DIR` | A folder to use in place of the `bugsplat` folder above |

## Sign in from CI

A build machine has no browser, so it authenticates with an OAuth Client ID and Client Secret. Create the pair as described in [OAuth2](oauth2.md#client-credentials). Each pair belongs to one database. A client with the **symbols** scope can upload symbols. To read crash data as well, create the client with the **restricted** scope.

Set the credentials as environment variables, or pass `--client-id` and `--client-secret`:

| Environment variable | Value |
| --- | --- |
| `BUGSPLAT_DATABASE` | Your database |
| `BUGSPLAT_CLIENT_ID` | The Client ID |
| `BUGSPLAT_CLIENT_SECRET` | The Client Secret |

The CLI also reads `SYMBOL_UPLOAD_CLIENT_ID` and `SYMBOL_UPLOAD_CLIENT_SECRET`, the variables the `symbol-upload` tool uses. When a Client ID and Client Secret are set, the CLI uses them and ignores any stored session.

These GitHub Actions steps install the CLI and upload the Windows symbols of a build:

```yaml
- name: Install the BugSplat CLI
  run: npm install -g @bugsplat/cli

- name: Upload symbols to BugSplat
  env:
    BUGSPLAT_DATABASE: ${{ secrets.BUGSPLAT_DATABASE }}
    BUGSPLAT_CLIENT_ID: ${{ secrets.BUGSPLAT_CLIENT_ID }}
    BUGSPLAT_CLIENT_SECRET: ${{ secrets.BUGSPLAT_CLIENT_SECRET }}
  run: bugsplat symbols upload -a MyApp -v 1.0.0 -d build/Release -f "**/*.{pdb,exe,dll}"
```

{% hint style="info" %}
On Linux, `bugsplat` verifies HTTPS connections with the CA certificates installed on the system. A minimal container image, such as `node:22-slim`, has none, and commands fail with `No CA certificates were loaded from the system`. Install the image's `ca-certificates` package first.
{% endhint %}

## Choose a database

Name the database with `--database` (`-b`) on any command, or set `BUGSPLAT_DATABASE`:

```bash
bugsplat groups list -b fred
```

`bugsplat databases list` lists the databases you can open and marks your default database. The commands that work with crash data (`reports`, `groups`, `versions`, `defects`, `symbols list`, and `tool call`) use your default database when you don't name one. `bugsplat symbols upload` and `bugsplat symbols delete` change one database's symbol files, so name the database for them.

## Commands

| Command | What it does |
| --- | --- |
| `bugsplat login`, `logout`, `whoami` | Sign in through your browser, sign out, and show who is signed in |
| `bugsplat databases list` | List the databases you can open |
| `bugsplat reports list`, `reports get <id>`, `reports reprocess <id>` | List crash reports, show one, or ask BugSplat to process one again |
| `bugsplat groups list`, `groups get <id>`, `groups set-status <id> <status>` | List crash groups, show one, or change its status |
| `bugsplat versions list` | List application versions |
| `bugsplat defects create`, `link`, `unlink` | File a crash group as a defect in your issue tracker, link it to a defect that already exists, or remove the link |
| `bugsplat tool list`, `tool call <name>` | List the tools of BugSplat's MCP server, or call one by name |
| `bugsplat symbols list` | List the symbol files uploaded to a database |
| `bugsplat symbols info <file>...` | Print each file's debug id, module name, and type |
| `bugsplat symbols find` | Search folders for symbol files that match debug ids or names |
| `bugsplat symbols upload`, `symbols delete` | Upload an application version's symbol files, or delete them |
| `bugsplat symbols dump <file>...` | Generate Breakpad `.sym` files from binaries, without uploading them |
| `bugsplat completions <shell>` | Print a completion script for bash, zsh, fish, PowerShell, or elvish |

## Crash data

The `databases`, `reports`, `groups`, `versions`, `defects`, and `symbols list` commands call the tools of BugSplat's hosted [MCP server](mcp-server/), so `bugsplat groups list` returns what a coding agent gets from the `list-groups` tool. The options are the tool's arguments, written with hyphens: the `stackKeyId` argument is `--stack-key-id`.

This command lists the crash groups in MyGame 2.1 that affected five or more users since November 1, with the most affected users first:

```bash
bugsplat groups list --application MyGame --version '2.1.*' --users '{"gte":5}' --start-date 2026-11-01 --sort-by users
```

This command lists the crash reports since November 16 from players with GeForce cards, except on version 1.0. `--attribute` matches a custom attribute, here `GPU`, and you can repeat it:

```bash
bugsplat reports list --attribute GPU='GeForce*' --version '{"ne":"1.0"}' --start-date 2026-11-16
```

A filter takes plain text, a JSON array of values, or a JSON object of operators such as `{"gte":5}`. Put a JSON filter in single quotes so your shell passes it through unchanged. [Filters](mcp-server/tools.md#filters) on the MCP Tools page lists the operators, and each command's `--help` lists its filters.

The list commands print one page at a time. `--page-size` sets the page length and `--page` picks the page.

`bugsplat reports get <id>` and `bugsplat groups get <id>` show one crash report or one crash group in full. The ids come from the list commands.

Some commands change data:

* `bugsplat groups set-status <id> <status>` marks a crash group `open`, `fixed`, `regression`, or `ignored`. The web app shows a fixed crash group as Closed.
* `bugsplat defects create <group-id>` files a crash group as a new defect in the issue tracker connected to the database. `defects link <group-id> <defect-id>` links the crash group to a defect that already exists, and `defects unlink <group-id>` removes the link.
* `bugsplat reports reprocess <id>` asks BugSplat to process a crash report again, for example after you upload its symbols.

`defects create`, `defects link`, and `reports reprocess --force` ask you to confirm at a terminal. In a script, add `--yes`.

## Text and JSON output

Commands print plain text by default. Add `--json` to any command to print JSON for a script:

```bash
bugsplat groups list --status open --json
```

For the commands that call an MCP tool, `--json` prints the JSON document the tool returns. Its field names follow the tool's argument names, such as `stackKeyId`, `application`, and `version`. A list command's document has the `database`, an array named for what the command lists (`reports`, `groups`, `versions`, or `symbols`), `page`, `pageSize`, and `hasMore`, which is `true` when there may be another page. [Output format](mcp-server/tools.md#output-format) on the MCP Tools page describes the documents.

Results go to stdout, and errors go to stderr. `--quiet` prints only results and errors, and `--verbose` prints more detail about what the command is doing.

## Call an MCP tool by name

`bugsplat tool list` lists the MCP server's tools and their arguments. `bugsplat tool call` calls one by name, which reaches every tool the server has, including a tool that has no command of its own:

```bash
bugsplat tool call list-groups application=MyGame pageSize=5
```

Arguments are `key=value` pairs. The CLI sends each value as the JSON type the tool accepts for that argument: `version=1.0` stays text, `pageSize=5` is a number, and `status='["open","fixed"]'` is an array. To pass the arguments as one JSON object, use `--args`:

```bash
bugsplat tool call list-groups --args '{"status":["open","regression"],"linkedDefect":false}'
```

[MCP Tools](mcp-server/tools.md) lists every tool and its arguments.

## Symbol files

### Inspect local files

`bugsplat symbols info` prints the module name, debug id, and type of each file you name: PE, PDB, portable PDB, ELF, Mach-O, or Breakpad `.sym`. The command also reads `.dSYM`, `.app`, and `.xcarchive` bundles.

```bash
bugsplat symbols info build/Release/MyApp.pdb
```

### Find files by debug id or name

`bugsplat symbols find` searches folders for symbol files by debug id or by name:

```bash
bugsplat symbols find --debug-id E546B55B6D214E86871B40AC35CD0D461 --path ./build --path ~/symbols
bugsplat symbols find --name myapp.pdb --path .
bugsplat symbols find --path ./build
```

| Option | What it does |
| --- | --- |
| `--debug-id <ID>` | Debug id to look for, in any case, with or without dashes. Repeat it for several ids. |
| `--name <NAME>` | File or module name to look for, in any case, with globs. Repeat it for several names. |
| `--path <DIR>` | Folder to search. Repeat it for several folders. Defaults to the current folder. |
| `--max-depth <N>` | How many levels below each `--path` to search. `1` is only the files directly in it. |
| `--no-ignore` | Also search `.git`, `node_modules`, and the other folders skipped by default |
| `--follow` | Follow symbolic links |

With no `--debug-id` or `--name`, the command lists every symbol file it finds. With both, it reports a file that matches either. The command exits with 1 when a requested id or name was not found.

### Upload

`bugsplat symbols upload` uploads the symbol files of one application version. Use the application name and version the app reports in crashes:

```bash
bugsplat symbols upload -a MyApp -v 1.0.0 -d build/Release -f "**/*.{pdb,exe,dll}"
```

| Option | What it does |
| --- | --- |
| `-a`, `--application` | Application name. Defaults to the `name` in `./package.json`. |
| `-v`, `--version` | Application version. Defaults to the `version` in `./package.json`. |
| `-d`, `--directory` | Base directory to search for symbol files. Defaults to the current directory. |
| `-f`, `--files` | Glob, relative to the directory, that selects the files to upload. Defaults to `*.js.map`, so a native build passes its own pattern. |
| `-w`, `--workers` | Number of files to upload at once. Defaults to the number of CPU cores or 8, whichever is larger. |
| `-l`, `--local-path` | Copy the files into a symbol server folder layout in this folder and upload nothing. See [Symbol Servers](../working-with-symbol-files/symbol-servers.md). |
| `--dry-run` | List the files that would be uploaded, with each file's debug id, and upload nothing |
| `-m`, `--dump-syms` | Generate a Breakpad `.sym` file from each selected binary and upload that in place of the binary |

Run the command with `--dry-run` first to check what a glob selects.

### Delete

`bugsplat symbols delete` deletes every symbol file of one application version, and requires `-a` and `-v`:

```bash
bugsplat symbols delete -a MyApp -v 1.0.0
```

The command asks you to confirm at a terminal. In a script, add `--yes`.

### List uploaded files

`bugsplat symbols list` lists the symbol files uploaded to a database. Filter the list with `--application`, `--version`, `--name`, and `--debug-id`:

```bash
bugsplat symbols list --application MyApp --version 1.0.0
```

### Breakpad .sym files

`bugsplat symbols dump` generates Breakpad `.sym` files with [dump_syms](https://github.com/mozilla/dump_syms) from ELF, Mach-O, PE, or PDB files, or from `.dSYM` bundles, and uploads nothing. `-o` names the folder to write the files to, which defaults to the current folder:

```bash
bugsplat symbols dump build/libmyapp.so -o syms
```

To generate and upload in one step, add `-m` (`--dump-syms`) to `bugsplat symbols upload`.

## Coming from symbol-upload

`bugsplat symbols upload` does what the [`symbol-upload` tool](../working-with-symbol-files/upload-symbols-with-symbol-upload.md) does and keeps its short flags, so most commands translate by changing the first words:

```bash
# before
npx @bugsplat/symbol-upload -b fred -a MyApp -v 1.0.0 -d build/Release -f "**/*.{pdb,exe,dll}"

# after
bugsplat symbols upload -b fred -a MyApp -v 1.0.0 -d build/Release -f "**/*.{pdb,exe,dll}"
```

| `symbol-upload` | `bugsplat symbols upload` |
| --- | --- |
| `-b`, `--database` | `-b`, `--database`. `BUGSPLAT_DATABASE` still works. |
| `-a`, `--application` | `-a`, `--application` |
| `-v`, `--version` | `-v`, `--version` |
| `-f`, `--files` | `-f`, `--files`. The default is still `*.js.map`. |
| `-d`, `--directory` | `-d`, `--directory`. The default is still the current directory. |
| `-w`, `--workers` | `-w`, `--workers` |
| `-l`, `--localPath` | `-l`, `--local-path` |
| `-i`, `--clientId` | `--client-id`, or `BUGSPLAT_CLIENT_ID`. `SYMBOL_UPLOAD_CLIENT_ID` still works. |
| `-s`, `--clientSecret` | `--client-secret`, or `BUGSPLAT_CLIENT_SECRET`. `SYMBOL_UPLOAD_CLIENT_SECRET` still works. |
| `-r`, `--remove` | A separate command: `bugsplat symbols delete -b fred -a MyApp -v 1.0.0` |
| `-m`, `--dumpSyms` | `-m`, `--dump-syms` |
| `-h`, `--help` | `-h`, `--help` |

What changes:

* Long options with two words are written with a hyphen: `--local-path`, `--client-id`, `--client-secret`.
* The Client ID and Client Secret have no short flags. Use the long options or the environment variables.
* `bugsplat symbols upload` uploads every file that has a debug id, so `.so`, `.dylib`, and extensionless binaries matched by your `-f` pattern upload too. The `symbol-upload` tool skips them.
* `bugsplat symbols delete` requires `-a` and `-v`, and asks you to confirm at a terminal. In a script, add `--yes`.

## Global options

These options work with every command:

| Option | Environment variable | What it does |
| --- | --- | --- |
| `--database`, `-b` | `BUGSPLAT_DATABASE` | The BugSplat database to use |
| `--client-id` | `BUGSPLAT_CLIENT_ID`, `SYMBOL_UPLOAD_CLIENT_ID` | OAuth Client ID for the database, for CI |
| `--client-secret` | `BUGSPLAT_CLIENT_SECRET`, `SYMBOL_UPLOAD_CLIENT_SECRET` | OAuth Client Secret for the database, for CI |
| `--api-url` | `BUGSPLAT_API_URL`, `BUGSPLAT_HOST` | Base URL of the BugSplat API. Defaults to `https://api.bugsplat.com`. |
| `--json` | | Print JSON in place of text |
| `--quiet`, `-q` | | Print only results and errors |
| `--verbose` | | Print more detail about what the command is doing |

## Exit codes

| Exit code | Meaning |
| --- | --- |
| 0 | The command succeeded |
| 1 | The command failed |
| 2 | Usage error, such as a missing argument or a filter that is not valid JSON |
| 3 | Not signed in. Run `bugsplat login`, or set a Client ID and Client Secret. |
