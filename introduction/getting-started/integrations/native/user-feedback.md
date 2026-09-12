---
description: >-
  Let users send bug reports and feature requests from inside your app with
  BugSplat Native 9.0; they appear next to crashes, grouped by title.
---

# User Feedback

Feedback is a report a user chose to send: a bug they noticed, a feature they want, a screenshot of something that looks wrong. BugSplat Native posts it through the same store and upload as a crash, as crash type 36 (`User.Feedback`), and it appears in the BugSplat app with the platform "User Feedback", grouped by title. The wire format is the one in [User Feedback (web services)](../../../development/web-services/user-feedback.md).

### Post feedback

```c
const char* files[] = { "C:/app/screenshot.png" };
bugsplat_upload_result r = BUGSPLAT_UPLOAD_RESULT_INIT;
bugsplat_result rc = bugsplat_post_feedback("Login button does nothing",
                                            "Tapping Login on the welcome screen has no effect.",
                                            files, 1, &r);
if (rc == BUGSPLAT_OK) { /* r.crash_id; r.info_url is the support response, if any */ }
bugsplat_upload_result_free(&r);
```

C++:

```cpp
auto result = bugsplat.PostFeedback("Login button does nothing", "Tapping Login has no effect.", {"C:/app/screenshot.png"});
```

.NET:

```csharp
var result = bugsplat.PostFeedback("Login button does nothing", "Tapping Login has no effect.", new[] { screenshotPath });
```

* **title** becomes the crash group (stack key); **description** becomes the exception message.
* Attachments are the files you pass; the session's crash attachments are not added automatically.
* User, email, key, environment and attributes come from the current values, so set them before posting if the form collected a name or email.
* With the `MANUAL` policy the report is left pending (result OK, crash id 0) for a later `bugsplat_send_report`.

### Building the form

The SDK has no feedback dialog of its own: your settings or help screen already has the right place for a form. Collect a title, a description, optionally name and email, and call `bugsplat_post_feedback` from a background thread (it blocks for the upload). Show `info_url` when it is returned; it is the [support response](../../../production/setting-up-custom-support-responses.md) configured for the key, so it can thank the user or point them at a workaround.

See also [Send Feedback](../../../../education/how-tos/sending-feedback.md) for how feedback looks in the app.
