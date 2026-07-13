# User Feedback

User feedback reports allow your application to collect non-crash feedback and bug reports from end users. Unlike crash reports that are generated automatically when an application fails, user feedback reports are submitted intentionally by users to describe issues, request features, or report bugs.

## How It Works

When a user feedback report is posted to BugSplat:

* The **title** becomes the stack key (crash group), making it easy to group and track related feedback
* The **description** becomes the exception message, providing additional context
* Reports appear alongside crash reports in the BugSplat dashboard with the platform label "User Feedback"

## File Formats

User feedback reports can be uploaded as either `feedback.xml` or `feedback.json`.

### XML

```xml
<?xml version="1.0" encoding="utf-8"?>
<feedback version="1">
  <title>Login button does not respond</title>
  <description>Tapping login on iPhone does nothing.</description>
</feedback>
```

{% hint style="info" %}
Special XML characters in title and description (`&`, `<`, `>`, `"`, `'`) must be escaped using standard XML entities.
{% endhint %}

### JSON

```json
{
  "title": "Login button does not respond",
  "description": "Tapping login on iPhone does nothing."
}
```

### Fields

| Field         | Required | Description                                         |
| ------------- | -------- | --------------------------------------------------- |
| `title`       | No\*     | Short summary of the feedback (becomes stack key); if omitted, a truncated `description` is used instead |
| `description` | No\*     | Detailed description of the issue (can be empty)    |

\* At least one of `title` or `description` must be provided.

## Uploading via Presigned URL

User feedback reports are uploaded using the same presigned URL flow as crash reports. See [Crash Post Endpoints](crash.md) for full details on Steps 1-3.

Use `crashType=User.Feedback` (or `crashTypeId=36`) when committing the upload in Step 3.

### Quick Reference

1. **Step 1** — `GET /api/getCrashUploadUrl` to get a presigned upload URL
2. **Step 2** — `PUT` the zipped `feedback.xml` (or `feedback.json`) to the presigned URL
3. **Step 3** — `POST /api/commitS3CrashUpload` with `crashType=User.Feedback`

Optional fields on commit: `user`, `email`, `description`, `appKey`, `attributes`

## Python Upload Example

```py
import hashlib
import os
import requests
import sys
import zipfile

# Usage: python upload_feedback.py <database> <application> <version> <feedback.xml|feedback.json>

def main():
    if len(sys.argv) != 5:
        print_usage()
        exit(1)

    database = sys.argv[1]
    application = sys.argv[2]
    version = sys.argv[3]
    file = sys.argv[4]

    if not os.path.exists(file):
        print(f"Error: The file {file} does not exist.")
        exit(1)

    if not (file.endswith(".xml") or file.endswith(".json")):
        print(f"Error: The file {file} must be feedback.xml or feedback.json.")
        exit(1)

    upload_user_feedback(database, application, version, file)


def upload_user_feedback(database, application, version, file):
    zip_file = ""
    try:
        zip_file = create_zip(file)
        file_size = os.path.getsize(zip_file)
        upload_url = get_crash_upload_url(database, application, version, file_size)
        crash_type = "User.Feedback"
        crash_type_id = 36
        upload_file_md5 = get_md5_hash(zip_file)
        upload_file_to_presigned_url(upload_url, zip_file)
        commit_s3_crash_upload(
            upload_url,
            database,
            application,
            version,
            crash_type,
            crash_type_id,
            upload_file_md5,
        )
        print("Uploaded user feedback successfully")
    except Exception as e:
        print(e)
    finally:
        if zip_file and os.path.exists(zip_file):
            os.remove(zip_file)


def commit_s3_crash_upload(s3_key, database, application, version, crash_type, crash_type_id, md5):
    route = f"https://{database}.bugsplat.com/api/commitS3CrashUpload"
    files = {
        "database": (None, database),
        "appName": (None, application),
        "appVersion": (None, version),
        "crashType": (None, crash_type),
        "crashTypeId": (None, str(crash_type_id)),
        "s3key": (None, s3_key),
        "md5": (None, md5),
    }

    response = requests.post(route, files=files)
    if response.status_code != 200:
        raise Exception(f"Failed to commit S3 crash upload: {response.text}")
    return response.json()


def create_zip(file):
    zip_filename = file + ".zip"
    with zipfile.ZipFile(zip_filename, "w", zipfile.ZIP_DEFLATED) as zipf:
        zipf.write(file, os.path.basename(file))
    return zip_filename


def get_crash_upload_url(database, application, version, size):
    route = f"https://{database}.bugsplat.com/api/getCrashUploadUrl?database={database}&appName={application}&appVersion={version}&crashPostSize={size}"
    response = requests.get(route)
    if response.status_code == 429:
        raise Exception("Failed to get crash upload URL, too many requests")
    if response.status_code != 200:
        raise Exception("Failed to get crash upload URL")
    json_response = response.json()
    return json_response["url"]


def get_md5_hash(file_path):
    hash_md5 = hashlib.md5()
    with open(file_path, "rb") as f:
        for chunk in iter(lambda: f.read(4096), b""):
            hash_md5.update(chunk)
    return hash_md5.hexdigest()


def print_usage():
    print("Usage: python upload_feedback.py <database> <application> <version> <feedback.xml|feedback.json>")


def upload_file_to_presigned_url(presigned_url, file_path, additional_headers={}):
    file_size = os.path.getsize(file_path)
    headers = {
        "content-type": "application/octet-stream",
        "content-length": str(file_size),
        **additional_headers,
    }
    with open(file_path, "rb") as file:
        response = requests.put(presigned_url, headers=headers, data=file)
        if response.status_code != 200:
            raise Exception(f"Error uploading to presigned URL {presigned_url}")
        return response


main()
```
