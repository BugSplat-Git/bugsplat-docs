# User Feedback

User feedback reports allow your application to collect non-crash feedback and bug reports from end users. Unlike crash reports that are generated automatically when an application fails, user feedback reports are submitted intentionally by users to describe issues, request features, or report bugs.

## How It Works

When a user feedback report is posted to BugSplat:

* The **title** becomes the stack key (crash group), making it easy to group and track related feedback
* The **description** becomes the exception message, providing additional context
* Reports appear alongside crash reports in the BugSplat dashboard with the platform label "User Feedback"

## XML Format

User feedback reports are uploaded as a file named `bsCrashReport.xml` with the following format:

```xml
<?xml version="1.0" encoding="utf-8"?>
<feedback version="1">
  <title>Login button does not respond</title>
  <description>Tapping login on iPhone does nothing.</description>
</feedback>
```

| Element       | Required | Description                                         |
| ------------- | -------- | --------------------------------------------------- |
| `title`       | Yes      | Short summary of the feedback (becomes stack key)   |
| `description` | No       | Detailed description of the issue (can be empty)    |

{% hint style="info" %}
Special XML characters in title and description (`&`, `<`, `>`, `"`, `'`) must be escaped using standard XML entities.
{% endhint %}

## Uploading via Presigned URL

User feedback reports are uploaded using the same presigned URL flow as crash reports. See [Crash Post Endpoints](crash.md) for full details on Steps 1-3.

Use `crashType=User.Feedback` (or `crashTypeId=36`) when committing the upload in Step 3.

### Quick Reference

1. **Step 1** — `GET /api/getCrashUploadUrl` to get a presigned upload URL
2. **Step 2** — `PUT` the zipped `bsCrashReport.xml` to the presigned URL
3. **Step 3** — `POST /api/commitS3CrashUpload` with `crashType=User.Feedback`

Optional fields on commit: `user`, `email`, `description`, `appKey`, `attributes`

## JS API Client

The [`bugsplat-js-api-client`](https://github.com/BugSplat-Git/bugsplat-js-api-client) package provides a `postUserFeedback()` helper that handles XML generation and upload:

```typescript
import { CrashPostClient } from 'bugsplat-js-api-client';
import { postUserFeedback } from 'bugsplat-js-api-client/src/post/user-feedback';

const client = new CrashPostClient('your-database');

await postUserFeedback(client, 'MyApp', '1.0.0', {
    title: 'Login button does not respond',
    description: 'Tapping login on iPhone does nothing.',
    user: 'jane@example.com',
    email: 'jane@example.com',
});
```
