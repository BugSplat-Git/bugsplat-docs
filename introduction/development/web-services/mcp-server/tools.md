---
description: >-
  The seven read-only tools of BugSplat's hosted MCP server, their arguments,
  and the filter syntax the list tools share.
---

# MCP Tools

BugSplat's [MCP server](README.md) has seven tools, and every tool only reads data. Your assistant picks a tool and fills in its arguments from your question, so you rarely type these yourself. This page shows what each tool can answer and how to check the arguments your assistant chose.

Every tool except `list-databases` takes a `database` argument. Leave it out and the tool reads your default database.

## Example questions

**"Which crash groups in MyGame 2.1 affected five or more users since November 1?"**

`list-groups`, sorted by affected users:

```json
{
  "application": "MyGame",
  "version": "2.1.*",
  "users": { "gte": 5 },
  "startDate": "2026-11-01",
  "sortBy": "users"
}
```

**"Show me this week's crashes from players with GeForce cards, except on version 1.0."**

`list-reports`, matching the `GPU` custom attribute:

```json
{
  "attributes": { "GPU": { "startsWith": "GeForce" } },
  "version": { "ne": "1.0" },
  "startDate": "2026-11-16"
}
```

**"Which open or regressed crash groups don't have a defect yet?"**

`list-groups`, with an array that matches either status:

```json
{
  "status": ["open", "regression"],
  "linkedDefect": false
}
```

## list-databases

Lists the databases you can open, in alphabetical order. Each line shows the company, whether you have full or restricted access, and which database is your default.

| Argument | Type | Description |
| -------- | ---- | ----------- |
| name | string | Text the database name contains (not case-sensitive) |
| company | string | Text the company name contains (not case-sensitive) |

## list-reports

Lists crash reports, newest first. Each report shows its id, crash time, application, version, the user's description, user, email, crash group, linked defects, and custom attributes.

| Argument | Type | Description |
| -------- | ---- | ----------- |
| database | string | Database to read |
| application | [text filter](#filters), pattern | Application name |
| version | [text filter](#filters), pattern | Application version |
| stackGroup | [text filter](#filters), pattern | Stack key of the crash group |
| stackKeyId | [integer filter](#filters) | Crash group id (see `list-groups`). `-1` lists reports that are not grouped yet. |
| user | [text filter](#filters), pattern | User value reported with the crash |
| email | [text filter](#filters), pattern | Email value reported with the crash |
| description | [text filter](#filters), contains | The user's description of the crash |
| exceptionCode | [text filter](#filters), exact | Exception code, for example `c0000005` |
| exceptionMessage | [text filter](#filters), contains | Exception message |
| attributes | object | [Custom attributes](#custom-attributes) to match |
| startDate | string | Only reports after this date |
| endDate | string | Only reports before this date |
| pageSize | integer | Reports per page, 1 to 100. Defaults to 10. |
| page | integer | Page to return, starting at 1 |
| sortBy | string | `date`: newest crash time first (default). `id`: highest crash id first. |
| sortOrder | string | `asc` or `desc`, to reverse the direction `sortBy` implies |

## get-report

Returns one crash report in full: its metadata, platform, exception, crash group and the group's status, linked defects, comments, custom attributes, the stack frames of the crashing thread (up to 100), and the report's event history.

| Argument | Type | Description |
| -------- | ---- | ----------- |
| id<mark style="color:red;">\*</mark> | integer | Crash id, from `list-reports` or `get-group` |
| database | string | Database to read |

## list-groups

Lists crash groups with their crash and user counts, first and last report times, status, support response, comments, and linked defects. The groups with the most crashes come first.

| Argument | Type | Description |
| -------- | ---- | ----------- |
| database | string | Database to read |
| application | [text filter](#filters), pattern | Application name |
| version | [text filter](#filters), pattern | Application version |
| status | [status filter](#filters) | `open`, `fixed`, `ignored`, or `regression` |
| stackKey | [text filter](#filters), contains | Text the stack key contains, such as a function or module name |
| crashes | [integer filter](#filters) | Number of crashes in the group, for example `{"gte": 10}` |
| users | [integer filter](#filters) | Number of affected users, for example `{"gte": 5}` |
| startDate | string | Count only reports after this date |
| endDate | string | Count only reports before this date |
| linkedDefect | boolean | `true`: only groups linked to a defect. `false`: only groups without one. |
| pageSize | integer | Groups per page, 1 to 100. Defaults to 10. |
| page | integer | Page to return, starting at 1 |
| sortBy | string | `crashes`: most crashes first (default). `users`: most affected users first. `newest`: most recent last report first. `oldest`: earliest first report first. |
| sortOrder | string | `asc` or `desc`, to reverse the direction `sortBy` implies |

## get-group

Returns one crash group: its crash and user counts, first and last report times, status, support response, comments, linked defects, most recent reports, and event history.

| Argument | Type | Description |
| -------- | ---- | ----------- |
| stackKeyId<mark style="color:red;">\*</mark> | integer | Crash group id, from `list-groups` |
| database | string | Database to read |
| pageSize | integer | Number of recent reports to include, 1 to 100. Defaults to 5. |

## list-versions

Lists the application versions in a database, most recently reported first. Each version shows its total crashes, reports per day, first and last report times, symbol storage size, and when symbols were last uploaded.

| Argument | Type | Description |
| -------- | ---- | ----------- |
| database | string | Database to read |
| application | [text filter](#filters), pattern | Application name |
| version | [text filter](#filters), pattern | Application version |
| includeRetired | boolean | Include retired versions, whose new crashes are rejected. Defaults to `false`. |
| startDate | string | Only versions whose last report is after this date |
| endDate | string | Only versions whose first report is before this date. With both dates, a version matches when the time between its first and last report overlaps the range. |
| pageSize | integer | Versions per page, 1 to 100. Defaults to 25. |
| page | integer | Page to return, starting at 1 |
| sortBy | string | `lastReport` (default), `firstReport`, `crashes`, `reportsPerDay`, `symbolSize`, or `symbolsUpdated` |
| sortOrder | string | `asc` or `desc`, to reverse the direction `sortBy` implies |

## list-symbols

Lists the symbol files uploaded to a database, most recently modified first. Each file shows its application and version, type, debug id, size, upload and modified times, when it was last used, and a download link.

| Argument | Type | Description |
| -------- | ---- | ----------- |
| database | string | Database to read |
| application | [text filter](#filters), pattern | Application name |
| version | [text filter](#filters), pattern | Application version |
| name | [text filter](#filters), contains | File name, for example `myapp.pdb` |
| debugId | [text filter](#filters), exact | Debug id (GUID and age) of the file |
| pageSize | integer | Files per page, 1 to 100. Defaults to 25. |
| page | integer | Page to return, starting at 1 |
| sortBy | string | `modified`: most recently modified first (default). `uploaded`: most recently uploaded first. `lastUsed`: most recently used first. `size`: largest first. `name`: file name A to Z. |
| sortOrder | string | `asc` or `desc`, to reverse the direction `sortBy` implies |

## Filters

Every filter argument takes one of three forms:

| Form | Example | Matches |
| ---- | ------- | ------- |
| Plain value | `"2.1.*"` | The filter's usual match (see below) |
| Array | `["open", "regression"]` | Any of the values |
| Object of operators | `{"gte": 10, "lt": 100}` | Only when every operator holds |

When you combine filters, a result has to match every filter.

### Plain text values

What a plain value matches depends on the filter, as the tables above show:

* **pattern**: an exact match, unless `*` starts or ends the value. `1.2.*` matches versions that start with `1.2.`, `*-beta` matches values that end with `-beta`, and `*steam*` matches values that contain `steam`. A `*` anywhere else is not allowed.
* **contains**: matches when the field contains the text.
* **exact**: an exact match. A `*` is treated as an ordinary character.

### Operators

Which operators a filter accepts depends on its type.

| Operator | Text | Integer | Status | Matches |
| -------- | ---- | ------- | ------ | ------- |
| `eq` | ✓ | ✓ | ✓ | Exactly this value |
| `ne` | ✓ | ✓ | ✓ | Anything but this value |
| `in` | ✓ | ✓ | ✓ | Any value in the array |
| `notIn` | ✓ | ✓ | ✓ | No value in the array |
| `contains` | ✓ | | | Text that contains the value |
| `notContains` | ✓ | | | Text that doesn't contain the value |
| `startsWith` | ✓ | | | Text that starts with the value |
| `endsWith` | ✓ | | | Text that ends with the value |
| `exists` | ✓ | | | `true`: any value. `false`: no value, or an empty one. |
| `gt` | | ✓ | | Greater than the value |
| `gte` | | ✓ | | Greater than or equal to the value |
| `lt` | | ✓ | | Less than the value |
| `lte` | | ✓ | | Less than or equal to the value |

For text filters:

* Operator values are literal text, so `{"startsWith": "1.2"}` and the plain value `"1.2*"` match the same versions.
* `ne`, `notIn`, and `notContains` also match reports that have no value at all.
* An empty string isn't accepted. To find reports with no value, use `{"exists": false}`.

### Custom attributes

`list-reports` filters on [custom attributes](../../../../education/how-tos/using-the-crash-attribute-feature.md) with the `attributes` argument, an object that maps each attribute name to a text filter:

```json
{
  "attributes": {
    "GPU": { "startsWith": "GeForce" },
    "platform": ["steam", "epic"]
  }
}
```

* A plain value follows the pattern rule above.
* Values compare as case-sensitive text, so only the text operators apply. `gt` and `lt` aren't available for attributes.
* Attribute names can contain letters, digits, spaces, `.`, `_`, and `-`.
* A name that belongs to a report field, such as the application name, is refused. Use the tool's own filter for that field.

## Paging, sorting, and dates

The list tools return one page at a time. Set the page length with `pageSize` (1 to 100) and the page with `page`, starting at 1. The last line of each page says whether there may be another page.

Each list tool has its own `sortBy` values, listed in its table. Every `sortBy` value implies a direction, such as newest first, and `sortOrder` (`asc` or `desc`) overrides it.

`startDate` and `endDate` take an ISO 8601 date such as `2026-11-01`, or a date and time such as `2026-11-01T12:00:00Z`. A time without a UTC offset is read as UTC.
