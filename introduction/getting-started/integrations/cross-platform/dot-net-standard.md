# .NET Standard

## Overview

BugSplatDotNetStandard lets you capture and track exceptions on all .NET Standard 2.0 [platforms](https://docs.microsoft.com/en-us/dotnet/standard/net-standard). This includes:

* .NET 5+
* .NET Framework
* Maui
* Mono
* Unity

### 🏗 Installation

Install the [BugSplatDotNetStandard](https://www.nuget.org/packages/BugSplatDotNetStandard/) NuGet package.

```powershell
Install-Package BugSplatDotNetStandard
```

### ⚙️ Configuration

After you've installed the NuGet package, add a using statement for the `BugSplatDotNetStandard` namespace.

```csharp
using BugSplatDotNetStandard;
```

Create a new instance of `BugSplat`, providing it with your database, application, and version. It's best to do this at the entry point of your application. Several defaults can be provided to BugSplat. You can provide default values for various fields, including description, email, key, notes, user, file attachments, and attributes.

```csharp
var bugsplat = new BugSplat(database, application, version);
bugsplat.Attachments.Add(new FileInfo("/path/to/attachment.txt"));
bugsplat.Description = "the default description";
bugsplat.Email = "fred@bugsplat.com";
bugsplat.Key = "the key!";
bugsplat.Notes = "the notes!";
bugsplat.User = "Fred";
bugsplat.Attributes.Add("key", "value");
```

The `Post` method can be used to send Exception objects to BugSplat.

```csharp
try
{
    throw new Exception("BugSplat rocks!");
}
catch(Exception ex)
{
    await bugsplat.Post(exception);
}
```

Additionally, `Post` can be used to upload minidumps to BugSplat.

```csharp
await bugsplat.Post(new FileInfo("/path/to/minidump.dmp"));
```

The default values for description, email, key, and user can be overridden in the call to Post. Additional attachments can also be specified in the call to the `Post` method. If BugSplat can't read an attachment (e.g., the file is in use), it will be skipped. Please note that the total size of the Post body and all attachments is limited to **100MB** by default.

```csharp
var options = new ExceptionPostOptions()
{
    Description = "BugSplat rocks!",
    Email = "fred@bugsplat.com",
    User = "Fred",
    Key = "the key!"
};
options.Attachments.Add(new FileInfo("/path/to/attachment2.txt"));

await bugsplat.Post(ex, options);
```

### ✅ Verification

Once you've generated an error, navigate to the BugSplat [Dashboard](https://app.bugsplat.com/v2/dashboard) and ensure you have the correct database selected in the dropdown menu. You should see a new crash report under the **Recent Crashes** section:

<figure><img src="https://private-user-images.githubusercontent.com/2646053/482744470-d8684cfe-83f0-49dd-b50c-2419e49d9431.png?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTYzMTA3OTcsIm5iZiI6MTc1NjMxMDQ5NywicGF0aCI6Ii8yNjQ2MDUzLzQ4Mjc0NDQ3MC1kODY4NGNmZS04M2YwLTQ5ZGQtYjUwYy0yNDE5ZTQ5ZDk0MzEucG5nP1gtQW16LUFsZ29yaXRobT1BV1M0LUhNQUMtU0hBMjU2JlgtQW16LUNyZWRlbnRpYWw9QUtJQVZDT0RZTFNBNTNQUUs0WkElMkYyMDI1MDgyNyUyRnVzLWVhc3QtMSUyRnMzJTJGYXdzNF9yZXF1ZXN0JlgtQW16LURhdGU9MjAyNTA4MjdUMTYwMTM3WiZYLUFtei1FeHBpcmVzPTMwMCZYLUFtei1TaWduYXR1cmU9YWZjZjg4ODRlYjExNmZlNmUyZGRmMWZiZGFiY2Q0ZTUzMTBmNTNlNjdiYzlmMjkzNDIzOWE5MDEwYjg3MzdmMSZYLUFtei1TaWduZWRIZWFkZXJzPWhvc3QifQ.GhonZJzIUxYf2puoXtKz1hyF9tKky_WRYogpciBwdeI" alt=""><figcaption></figcaption></figure>

Click the link in the **ID** column to see details about the crash:

<figure><img src="https://private-user-images.githubusercontent.com/2646053/482745479-deacdc98-40cd-4f85-bec1-c457c97a6932.png?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTYzMTA3OTcsIm5iZiI6MTc1NjMxMDQ5NywicGF0aCI6Ii8yNjQ2MDUzLzQ4Mjc0NTQ3OS1kZWFjZGM5OC00MGNkLTRmODUtYmVjMS1jNDU3Yzk3YTY5MzIucG5nP1gtQW16LUFsZ29yaXRobT1BV1M0LUhNQUMtU0hBMjU2JlgtQW16LUNyZWRlbnRpYWw9QUtJQVZDT0RZTFNBNTNQUUs0WkElMkYyMDI1MDgyNyUyRnVzLWVhc3QtMSUyRnMzJTJGYXdzNF9yZXF1ZXN0JlgtQW16LURhdGU9MjAyNTA4MjdUMTYwMTM3WiZYLUFtei1FeHBpcmVzPTMwMCZYLUFtei1TaWduYXR1cmU9ZmQ1M2M5N2I4Mjc1NmFiYTAzM2EwZGY5OTVmNjU4ODU4NTAyNTRmMGRlNDIzZDRiNGJjOWQzOTMzZjFmOGUxNCZYLUFtei1TaWduZWRIZWFkZXJzPWhvc3QifQ.uy_GWPbKv6-JnZ0YOrrQU22z9N0smPidZG8bmyw9SVI" alt=""><figcaption></figcaption></figure>

That’s it! Your application is now configured to post crash reports to BugSplat.

### 👷 Support

If you have any additional questions, please email our [support](mailto:support@bugsplat.com) team, join us on [Discord](https://discord.gg/K4KjjRV5ve), or reach out via the chat in our web application.
