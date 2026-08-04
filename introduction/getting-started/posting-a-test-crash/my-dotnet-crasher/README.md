# MyDotnetCrasher (.NET)

[MyDotnetCrasher](https://github.com/BugSplat-Git/my-dotnet-crasher) is a sample .NET 8 console application that demonstrates BugSplat crash reporting. It triggers a variety of .NET exceptions, captures them, and uploads the resulting crash reports so you can see how BugSplat symbolicates and groups them — without changing any of your own code.

Under the hood it uses [BugSplatDotNetStandard](https://github.com/BugSplat-Git/bugsplat-dotnet-standard), the same library you'd add to your own application. For a full integration guide, see [.NET Standard](../../integrations/desktop/dot-net-standard.md).

### Prerequisites 🚦

* [.NET 8.0 SDK](https://dotnet.microsoft.com/download/dotnet/8.0) or later
* A [BugSplat account](https://app.bugsplat.com/v2/sign-up) and a [database](../../create-a-new-database-in-bugsplat.md)
* Windows (required for symbol uploads)

### 1. Clone the sample

```bash
git clone https://github.com/BugSplat-Git/my-dotnet-crasher.git
cd my-dotnet-crasher
```

### 2. Configure your BugSplat database

Update the credentials in two places so reports and symbols land in your database.

**`Program.cs`** — set the database, application, and version on the `Reporter`:

```csharp
private static Reporter reporter = new Reporter("your-database", "MyDotnetCrasher", "1.0.0");
```

**`MyDotnetCrasher.csproj`** — update the symbol upload step in the `UploadSymbols` target so your crashes include file names and line numbers:

```xml
<Exec Command=".\Tools\symbol-upload-windows.exe -b your-database -a MyDotnetCrasher -v 1.0.0 -u your-email@example.com -p your-password -f &quot;**/*.{pdb,exe,dll}&quot; -d &quot;./bin&quot;"/>
```

{% hint style="info" %}
Prefer not to keep a password in your build? symbol-upload also authenticates with OAuth client credentials, which you can create on the [Integrations](https://app.bugsplat.com/v2/database/integrations#oauth) page. See [working with symbol files](../../../development/working-with-symbol-files/) for details.
{% endhint %}

### 3. Build

```bash
dotnet build
```

Building restores [BugSplatDotNetStandard](https://github.com/BugSplat-Git/bugsplat-dotnet-standard), compiles the app, and automatically uploads its debug symbols (PDBs) to BugSplat.

### 4. Post a test crash

Run the app with a crash type to generate a report:

```bash
dotnet run nullref     # NullReferenceException
dotnet run divzero     # DivideByZeroException
dotnet run index       # IndexOutOfRangeException
dotnet run aggregate   # AggregateException (multiple errors)
dotnet run unobserved  # unobserved async task exception
dotnet run exception   # generic exception (the default)
```

The app catches the exception with a global handler, generates a Windows minidump, uploads the crash to BugSplat, and exits.

### 5. View the crash ✅

Open the [Crashes](https://app.bugsplat.com/v2/crashes) page and select your database. Click a report's **ID** to see the symbolicated call stack — with file names and line numbers from the uploaded symbols — along with the exception message, the minidump, and system information. BugSplat automatically [groups](../../../development/grouping-crashes.md) similar crashes so you can prioritize by impact.

{% hint style="info" %}
Not seeing file names and line numbers? Confirm the symbol upload completed during `dotnet build`, and that the database, application, and version match between `Program.cs` and the symbol-upload command.
{% endhint %}
