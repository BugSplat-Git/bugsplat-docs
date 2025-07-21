# Unity

### 🏗 Installation

BugSplat's `com.bugsplat.unity` package can be added to your project via [OpenUPM](https://openupm.com/packages/com.bugsplat.unity/) or a URL to our git [repository](https://github.com/BugSplat-Git/bugsplat-unity.git).

#### OpenUPM

Information on installing OpenUPM can be found [here](https://openupm.com/). After installing OpenUPM, run the following command to add BugSplat to your project.

```bash
openupm add com.bugsplat.unity
```

#### Git

Information on adding a Unity package via a git URL can be found [here](https://docs.unity3d.com/Manual/upm-ui-giturl.html).

```
https://github.com/BugSplat-Git/bugsplat-unity.git
```

### 🧑‍🏫 Sample

{% hint style="success" %}
BugSplat recommends building with the IL2CPP backend for the best crash reporting experience. For more information, please see the [Player Settings](unity.md#player-settings) section.
{% endhint %}

After installing `com.bugsplat.unity`, you can import a sample project to help you get started with BugSplat. Click here if you'd like to skip the sample project and get straight to the [usage](unity.md#usage) instructions.

To import the sample, click the caret next to **Samples** to reveal the **my-unity-crasher** sample. Click **Import** to add the sample to your project.

<figure><img src="https://private-user-images.githubusercontent.com/2646053/310597588-b7a39388-eb76-413a-a92f-72fd39c9a7d6.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTMxMzUzOTcsIm5iZiI6MTc1MzEzNTA5NywicGF0aCI6Ii8yNjQ2MDUzLzMxMDU5NzU4OC1iN2EzOTM4OC1lYjc2LTQxM2EtYTkyZi03MmZkMzljOWE3ZDYucG5nP1gtQW16LUFsZ29yaXRobT1BV1M0LUhNQUMtU0hBMjU2JlgtQW16LUNyZWRlbnRpYWw9QUtJQVZDT0RZTFNBNTNQUUs0WkElMkYyMDI1MDcyMSUyRnVzLWVhc3QtMSUyRnMzJTJGYXdzNF9yZXF1ZXN0JlgtQW16LURhdGU9MjAyNTA3MjFUMjE1ODE3WiZYLUFtei1FeHBpcmVzPTMwMCZYLUFtei1TaWduYXR1cmU9OTM2YTI2ZGQwOTViZWVhZGNjMTMzYzZhMDMzMzhkNTZlMWExNzE2ODVhZWIzNTE2Y2ZjYmM3MGRkMjI3YWY1NyZYLUFtei1TaWduZWRIZWFkZXJzPWhvc3QifQ.JKPvmnWOZ1KfJRMuN59JNeujQERSRraAc1ZJk5oLEdo" alt=""><figcaption></figcaption></figure>

In the Project Assets browser, open the **Sample** scene from `Samples > BugSplat > Version > my-unity-crasher > Scenes`.

Next, select `Samples > BugSplat > Version > my-unity-crasher` to reveal the **BugSplatOptions** object. Click BugSplatOptions and replace the database value with your BugSplat database.

<figure><img src="https://private-user-images.githubusercontent.com/2646053/310597753-ba9aa64a-1d85-45a8-b11f-565520c30bcf.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTMxMzUzOTcsIm5iZiI6MTc1MzEzNTA5NywicGF0aCI6Ii8yNjQ2MDUzLzMxMDU5Nzc1My1iYTlhYTY0YS0xZDg1LTQ1YTgtYjExZi01NjU1MjBjMzBiY2YucG5nP1gtQW16LUFsZ29yaXRobT1BV1M0LUhNQUMtU0hBMjU2JlgtQW16LUNyZWRlbnRpYWw9QUtJQVZDT0RZTFNBNTNQUUs0WkElMkYyMDI1MDcyMSUyRnVzLWVhc3QtMSUyRnMzJTJGYXdzNF9yZXF1ZXN0JlgtQW16LURhdGU9MjAyNTA3MjFUMjE1ODE3WiZYLUFtei1FeHBpcmVzPTMwMCZYLUFtei1TaWduYXR1cmU9YmQ3ZGI4YTY5NTc2MzI4YjdiYzdkM2M0ZGNmNTAzZDY3ZWJhYzU3NGZiMDJmMjQwMGNmMTA1NDQ3MzU4NzI3NSZYLUFtei1TaWduZWRIZWFkZXJzPWhvc3QifQ.4VSatL8gp_S9U-FK0-E4CXpDf5I7jXnQsujQtrSBY0s" alt=""><figcaption></figcaption></figure>

<figure><img src="https://private-user-images.githubusercontent.com/2646053/310597818-a6250cea-a4da-44a8-b6cb-ff2467b0d978.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTMxMzUzOTcsIm5iZiI6MTc1MzEzNTA5NywicGF0aCI6Ii8yNjQ2MDUzLzMxMDU5NzgxOC1hNjI1MGNlYS1hNGRhLTQ0YTgtYjZjYi1mZjI0NjdiMGQ5NzgucG5nP1gtQW16LUFsZ29yaXRobT1BV1M0LUhNQUMtU0hBMjU2JlgtQW16LUNyZWRlbnRpYWw9QUtJQVZDT0RZTFNBNTNQUUs0WkElMkYyMDI1MDcyMSUyRnVzLWVhc3QtMSUyRnMzJTJGYXdzNF9yZXF1ZXN0JlgtQW16LURhdGU9MjAyNTA3MjFUMjE1ODE3WiZYLUFtei1FeHBpcmVzPTMwMCZYLUFtei1TaWduYXR1cmU9YzkwYjJiMGE2Y2UzMDA0ZWQzMzU2Zjg2NzhlYjEyODcyYjE4NzU3YWI4MTMyZjRlMmY3NmVjMWRmZmU3N2VlNCZYLUFtei1TaWduZWRIZWFkZXJzPWhvc3QifQ.J2FY2qvUXyAxXL7brX-GCjUyfAL8oIaMmiNDt8lRw1o" alt=""><figcaption></figcaption></figure>

Click **Play** and click or tap one of the buttons to send an error report to BugSplat. To view the error report, navigate to the BugSplat [Dashboard](https://app.bugsplat.com/v2/dashboard) and ensure you have selected the correct database.

<figure><img src="https://private-user-images.githubusercontent.com/2646053/310597931-4418b736-dc88-496a-ada6-a27ad19032f1.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTMxMzUzOTcsIm5iZiI6MTc1MzEzNTA5NywicGF0aCI6Ii8yNjQ2MDUzLzMxMDU5NzkzMS00NDE4YjczNi1kYzg4LTQ5NmEtYWRhNi1hMjdhZDE5MDMyZjEucG5nP1gtQW16LUFsZ29yaXRobT1BV1M0LUhNQUMtU0hBMjU2JlgtQW16LUNyZWRlbnRpYWw9QUtJQVZDT0RZTFNBNTNQUUs0WkElMkYyMDI1MDcyMSUyRnVzLWVhc3QtMSUyRnMzJTJGYXdzNF9yZXF1ZXN0JlgtQW16LURhdGU9MjAyNTA3MjFUMjE1ODE3WiZYLUFtei1FeHBpcmVzPTMwMCZYLUFtei1TaWduYXR1cmU9OGY3MzhiZDk2ZjUwMjQ4NzliNWJiN2ViM2ZhMmFkMWRhZDcxZmNkNzU1ZGNkODA0NjM2NWFmNzU1ZmI5MzRhYyZYLUFtei1TaWduZWRIZWFkZXJzPWhvc3QifQ.5fakPEkpZVlQkoGY1ywKACSbbeLMlkrmg-VgGIgxIzY" alt=""><figcaption></figcaption></figure>

Navigate to the [Crashes](https://app.bugsplat.com/v2/crashes) page, and click the value in the ID column to see the details of your report, including the call stack, log file, and screenshot of your app when the error occurred.

<figure><img src="https://private-user-images.githubusercontent.com/2646053/310613142-f108d7e9-ee90-4a09-a7b4-8a9b5d764942.gif?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTMxMzUzOTcsIm5iZiI6MTc1MzEzNTA5NywicGF0aCI6Ii8yNjQ2MDUzLzMxMDYxMzE0Mi1mMTA4ZDdlOS1lZTkwLTRhMDktYTdiNC04YTliNWQ3NjQ5NDIuZ2lmP1gtQW16LUFsZ29yaXRobT1BV1M0LUhNQUMtU0hBMjU2JlgtQW16LUNyZWRlbnRpYWw9QUtJQVZDT0RZTFNBNTNQUUs0WkElMkYyMDI1MDcyMSUyRnVzLWVhc3QtMSUyRnMzJTJGYXdzNF9yZXF1ZXN0JlgtQW16LURhdGU9MjAyNTA3MjFUMjE1ODE3WiZYLUFtei1FeHBpcmVzPTMwMCZYLUFtei1TaWduYXR1cmU9NjExN2NmNWVmYzU0MTYyYzJhM2I2ZjhmODFhMWMwZGRkMGJhZjhiZTk2MzRhOGVjZTI2ZTIxOWEyYzUzMmE0NSZYLUFtei1TaWduZWRIZWFkZXJzPWhvc3QifQ.mNUCZpybpTMXRrHSsYMTlxnbt4E49gllJ-NIlBnY3GA" alt=""><figcaption></figcaption></figure>

### 🧰 Player Settings

For best results, BugSplat recommends building with the `IL2CPP` backend. The `Mono` backend is supported, but has several limitations. With `IL2CPP`, BugSplat can capture fully symbolicated C# exception traces in production, as well as native crashes that contain call stacks mapped back to their original C# function names, file names, and line numbers.

To optimize your game for crash reporting, open `Player Settings` (`Edit > Player Settings`). Navigate to the `Configuration` section. For `Scripting Backend` choose `IL2CPP` and for `IL2CPP StackTrace Information` choose `Method Name, File Name, and Line Number`.

<figure><img src="https://private-user-images.githubusercontent.com/2646053/446326500-ed459d7e-8580-4e8d-b6aa-386ecaa51a56.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTMxMzUzOTcsIm5iZiI6MTc1MzEzNTA5NywicGF0aCI6Ii8yNjQ2MDUzLzQ0NjMyNjUwMC1lZDQ1OWQ3ZS04NTgwLTRlOGQtYjZhYS0zODZlY2FhNTFhNTYucG5nP1gtQW16LUFsZ29yaXRobT1BV1M0LUhNQUMtU0hBMjU2JlgtQW16LUNyZWRlbnRpYWw9QUtJQVZDT0RZTFNBNTNQUUs0WkElMkYyMDI1MDcyMSUyRnVzLWVhc3QtMSUyRnMzJTJGYXdzNF9yZXF1ZXN0JlgtQW16LURhdGU9MjAyNTA3MjFUMjE1ODE3WiZYLUFtei1FeHBpcmVzPTMwMCZYLUFtei1TaWduYXR1cmU9OTJjZTdjODM2NDk3MWE5ZWMwNjFhNTcwM2ZlZGRiNjE2OTAyYWJlYjUzNDhkNTVkNzAxMjUzNjdjZGRkNTAyYyZYLUFtei1TaWduZWRIZWFkZXJzPWhvc3QifQ.3CyP8WO0eS9d2kYZ3EfjIcev77AO-UXThgRRKzxGfCI" alt=""><figcaption></figcaption></figure>

### ⚙️ Configuration

BugSplat's Unity integration is flexible and can be used in various ways. The easiest way to get started is to attach the `BugSplatManager` Monobehaviour to a GameObject.

<figure><img src="https://private-user-images.githubusercontent.com/2646053/310598099-ef5240a6-9676-43c6-a482-51216cb34401.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTMxMzUzOTcsIm5iZiI6MTc1MzEzNTA5NywicGF0aCI6Ii8yNjQ2MDUzLzMxMDU5ODA5OS1lZjUyNDBhNi05Njc2LTQzYzYtYTQ4Mi01MTIxNmNiMzQ0MDEucG5nP1gtQW16LUFsZ29yaXRobT1BV1M0LUhNQUMtU0hBMjU2JlgtQW16LUNyZWRlbnRpYWw9QUtJQVZDT0RZTFNBNTNQUUs0WkElMkYyMDI1MDcyMSUyRnVzLWVhc3QtMSUyRnMzJTJGYXdzNF9yZXF1ZXN0JlgtQW16LURhdGU9MjAyNTA3MjFUMjE1ODE3WiZYLUFtei1FeHBpcmVzPTMwMCZYLUFtei1TaWduYXR1cmU9NmQ0NDJmMjdiMWZlYjFjOWI3ZmNmZmQ3ZDA5YjViYWZhMGJmY2E5MTdhYzUxNmMwYjMyNjBjZDk5MWYwZGZmYiZYLUFtei1TaWduZWRIZWFkZXJzPWhvc3QifQ.e1KnMg9JmGIsFAuwAsXL8N8oyFrA85SR2suxovL-KyA" alt=""><figcaption></figcaption></figure>

`BugSplatManager` needs to be initialized with a `BugSplatOptions` serialized object. A new instance of `BugSplatOptions` can be created through the Asset Create menu.

<figure><img src="https://private-user-images.githubusercontent.com/2646053/310598632-9ec402d1-4b8a-49cf-96e9-00d951717771.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTMxMzUzOTcsIm5iZiI6MTc1MzEzNTA5NywicGF0aCI6Ii8yNjQ2MDUzLzMxMDU5ODYzMi05ZWM0MDJkMS00YjhhLTQ5Y2YtOTZlOS0wMGQ5NTE3MTc3NzEucG5nP1gtQW16LUFsZ29yaXRobT1BV1M0LUhNQUMtU0hBMjU2JlgtQW16LUNyZWRlbnRpYWw9QUtJQVZDT0RZTFNBNTNQUUs0WkElMkYyMDI1MDcyMSUyRnVzLWVhc3QtMSUyRnMzJTJGYXdzNF9yZXF1ZXN0JlgtQW16LURhdGU9MjAyNTA3MjFUMjE1ODE3WiZYLUFtei1FeHBpcmVzPTMwMCZYLUFtei1TaWduYXR1cmU9MDczZTg1ODE0NzgzMWM1YjAwOWZhZmFmNmRiNjQ5MDZkZDk3ZjNiZmMyMzVmODA4Yzc0MmRiZjllY2VlNGNhOCZYLUFtei1TaWduZWRIZWFkZXJzPWhvc3QifQ.eOBRwjr09tIoVF865hn8FtLF8_l7OT3igB8fkDoVYJ8" alt=""><figcaption></figcaption></figure>

Configure fields as appropriate. Note that if Application or Version are left empty, `BugSplat` will default these values to `Application.productName` and `Application.version`, respectively.

<figure><img src="https://private-user-images.githubusercontent.com/2646053/310598526-be7ee217-9170-48b4-b780-fcb47e221f77.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTMxMzUzOTcsIm5iZiI6MTc1MzEzNTA5NywicGF0aCI6Ii8yNjQ2MDUzLzMxMDU5ODUyNi1iZTdlZTIxNy05MTcwLTQ4YjQtYjc4MC1mY2I0N2UyMjFmNzcucG5nP1gtQW16LUFsZ29yaXRobT1BV1M0LUhNQUMtU0hBMjU2JlgtQW16LUNyZWRlbnRpYWw9QUtJQVZDT0RZTFNBNTNQUUs0WkElMkYyMDI1MDcyMSUyRnVzLWVhc3QtMSUyRnMzJTJGYXdzNF9yZXF1ZXN0JlgtQW16LURhdGU9MjAyNTA3MjFUMjE1ODE3WiZYLUFtei1FeHBpcmVzPTMwMCZYLUFtei1TaWduYXR1cmU9OTU1M2IzYTM3OGU1OTA1NmQyYmYwNzQwYWM0ZjY3NGRkODIwNjU3ZWEyYzFjNmYxN2ZjMWIwOWE3YmJhNTkyYyZYLUFtei1TaWduZWRIZWFkZXJzPWhvc3QifQ.Oh2TwvyvYM7-1J_PDA6av-jZPdfce4npL3cguRbaV0U" alt=""><figcaption></figcaption></figure>

Finally, provide a valid `BugSplatOptions` to `BugSplatManager`.

<figure><img src="https://private-user-images.githubusercontent.com/2646053/310598782-67bed7b5-e2a9-4f52-b5bb-bdc8eebd35a0.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTMxMzUzOTcsIm5iZiI6MTc1MzEzNTA5NywicGF0aCI6Ii8yNjQ2MDUzLzMxMDU5ODc4Mi02N2JlZDdiNS1lMmE5LTRmNTItYjViYi1iZGM4ZWViZDM1YTAucG5nP1gtQW16LUFsZ29yaXRobT1BV1M0LUhNQUMtU0hBMjU2JlgtQW16LUNyZWRlbnRpYWw9QUtJQVZDT0RZTFNBNTNQUUs0WkElMkYyMDI1MDcyMSUyRnVzLWVhc3QtMSUyRnMzJTJGYXdzNF9yZXF1ZXN0JlgtQW16LURhdGU9MjAyNTA3MjFUMjE1ODE3WiZYLUFtei1FeHBpcmVzPTMwMCZYLUFtei1TaWduYXR1cmU9MmQ0MjBhM2UxMzA3NWI4NmNhZDJlZWRjZTJiZWM4YmZjOWE0NDkwNTVmYWRiYjlmNTlkMDNiNmRmMmQ0NmVlOSZYLUFtei1TaWduZWRIZWFkZXJzPWhvc3QifQ.VUjknjxaR-t8qO2dVJwKoaGNQS247XffLYBE5E-Vcj8" alt=""><figcaption></figcaption></figure>

### ⌨️ Usage

If you're using `BugSplatOptions` and `BugSplatManager`, BugSplat automatically configures an `Application.logMessageReceived` handler that will post reports when it encounters a log message of type `Exception`. You can also extend your BugSplat integration and [customize report metadata](unity.md#adding-metadata), [report exceptions in try/catch blocks](unity.md#try-catch-reporting), [prevent repeated reports](unity.md#preventing-repeated-reports), and [upload windows minidumps](unity.md#windows-minidumps-crashes) from native crashes.

#### Adding Metadata

First, find your instance of `BugSplat`. The following is an example of how to find an instance of `BugSplat` via `BugSplatManager`:

```csharp
var bugsplat = FindObjectOfType<BugSplatManager>().BugSplat;
```

You can extend `BugSplat` by setting the following properties:

```csharp
bugsplat.Attachments.Add(new FileInfo("/path/to/attachment.txt"));
bugsplat.Description = "description!";
bugsplat.Email = "fred@bugsplat.com";
bugsplat.Key = "key!";
bugsplat.Notes = "notes!";
bugsplat.User = "Fred";
bugsplat.CaptureEditorLog = true;
bugsplat.CapturePlayerLog = false;
bugsplat.CaptureScreenshots = true;
```

You can use the `Notes` field to capture arbitrary data such as system information:

```csharp
void Start()
{
    bugsplat = FindObjectOfType<BugSplatManager>().BugSplat;
    bugsplat.Notes = GetSystemInfo();
}

private string GetSystemInfo()
{
    var info = new Dictionary<string, string>();
    info.Add("OS", SystemInfo.operatingSystem);
    info.Add("CPU", SystemInfo.processorType);
    info.Add("MEMORY", $"{SystemInfo.systemMemorySize} MB");
    info.Add("GPU", SystemInfo.graphicsDeviceName);
    info.Add("GPU MEMORY", $"{SystemInfo.graphicsMemorySize} MB");

    var sections = info.Select(section => $"{section.Key}: {section.Value}");
    return string.Join(Environment.NewLine, sections);
}
```

#### Try/Catch Reporting

Exceptions can be sent to BugSplat in a try/catch block by calling `Post`.

```csharp
try
{
    throw new Exception("BugSplat rocks!");
}
catch (Exception ex)
{
    StartCoroutine(bugsplat.Post(ex));
}
```

The default values specified on the instance of `BugSplat` can be overridden in the call to `Post`. Additionally, you can provide a `callback` to `Post` that will be invoked with the result once the upload is complete.

```csharp
var options = new ReportPostOptions()
{
    Description = "a new description",
    Email = "barney@bugsplat.com",
    Key = "a new key!",
    Notes = "some new notes!",
    User = "Barney"
};

options.AdditionalAttachments.Add(new FileInfo("/path/to/additional.txt"));

static void callback()
{
    Debug.Log($"Exception post callback!");
};

StartCoroutine(bugsplat.Post(ex, options, callback));
```

#### Preventing Repeated Reports

By default, BugSplat prevents reports from being sent at a rate greater than 1 per every 3 seconds. You can override the default crash report throttling implementation by setting `ShouldPostException` on your BugSplat instance. To override `ShouldPostException`, assign the property a new `Func<Exception, bool>` value. Be sure your new implementation can handle a null value for `Exception`!

The following example demonstrates how you could implement your own time-based report throttling mechanism:

```csharp
var lastPost = new DateTime(0);

bugsplat.ShouldPostException = (ex) =>
{
    var now = DateTime.Now;

    if (now - lastPost < TimeSpan.FromSeconds(3))
    {
        Debug.LogWarning("ShouldPostException returns false. Skipping BugSplat report...");
        return false;
    }

    Debug.Log("ShouldPostException returns true. Posting BugSplat report...");
    lastPost = now;

    return true;
};
```

#### Windows Minidumps (Crashes)

BugSplat can be configured to upload Windows minidumps created by the `UnityCrashHandler`. BugSplat will automatically pull Unity Player symbols from the [Unity Symbol Server](https://docs.unity3d.com/Manual/WindowsDebugging.html).

The methods `PostCrash`, `PostMostRecentCrash`, and `PostAllCrashes` can be used to upload minidumps to BugSplat. We recommend running `PostAllCrashes` when your game launches.

```csharp
void Start()
{
    bugsplat = FindObjectOfType<BugSplatManager>().BugSplat;
    StartCoroutine(bugsplat.PostAllCrashes());
}
```

Each of the methods that post crashes to BugSplat also accept a `MinidumpPostOptions` parameter and a `callback`. The usage of `MinidumpPostOptions` and `callback` are nearly identical to the `ExceptionPostOptions` example listed above.

You can generate a test crash on Windows with any of the following methods.

```csharp
Utils.ForceCrash(ForcedCrashCategory.Abort);
Utils.ForceCrash(ForcedCrashCategory.AccessViolation);
Utils.ForceCrash(ForcedCrashCategory.FatalError);
Utils.ForceCrash(ForcedCrashCategory.PureVirtualFunction);
```

#### Windows Symbols

To enable the uploading of plugin symbols, generate an OAuth2 Client ID and Client Secret on the BugSplat [Integrations](https://app.bugsplat.com/v2/settings/database/integrations) page. Add your Client ID and Client Secret to the `BugSplatOptions` object you generated in the [Configuration](unity.md#configuration) section. If your game contains Native Windows C++ plugins, `.dll` and `.pdb` files in the `Assets/Plugins/x86` and `Assets/Plugins/x86_64` folders will be uploaded by BugSplat's PostBuild script and used in symbolication.

For IL2CPP builds, BugSplat will also upload `LineNumberMappings.json`. Line mappings allow BugSplat to replace generated C++ function names, file names, and line numbers with their original C# equivalents.

#### Support Response

BugSplat has the ability to display a support response to users who encounter a crash. You can show your users a generalized support response for all crashes, or a custom support response that corresponds to the type of crash that occurred. Defining a support response allows you to alert users that bug has been fixed in a new version, or that they need to update their graphics drivers.

Next, pass a callback to `bugsplat.Post`. In the callback handler add code to open the support response in the user's browser. A full example can be seen in [ErrorGenerator.cs](https://github.com/BugSplat-Git/bugsplat-unity/blob/main/Samples~/my-unity-crasher/Scripts/ErrorGenerator.cs).

```csharp
private string infoUrl = "";

public void Event_CatchExceptionThenPostNewBugSplat()
{
    try
    {
        GenerateSampleStackFramesAndThrow();
    }
    catch (Exception ex)
    {
        var options = new ReportPostOptions()
        {
            Description = "a new description"
        };

        StartCoroutine(bugsplat.Post(ex, options, ExceptionCallback));
    }
}

void ExceptionCallback(ExceptionReporterPostResult result)
{
    UnityEngine.Debug.Log($"Exception post callback result: {result.Message}");

    if (result.Response == null) {
        return;
    }

    UnityEngine.Debug.Log($"BugSplat Status: {result.Response.status}");
    UnityEngine.Debug.Log($"BugSplat Crash ID: {result.Response.crashId}");
    UnityEngine.Debug.Log($"BugSplat Support URL: {result.Response.infoUrl}");

    infoUrl = result.Response.infoUrl;
}

private void OpenUrl(string url)
{
    var escaped = url.Replace("?", "\\?").Replace("&", "\\&").Replace(" ", "%20").Replace("!", "\\!");

#if UNITY_STANDALONE_WIN || UNITY_EDITOR_WIN || UNITY_WSA
    Process.Start(url);
#elif UNITY_STANDALONE_OSX || UNITY_EDITOR_OSX
    Process.Start("open", escaped);
#elif UNITY_STANDALONE_LINUX || UNITY_EDITOR_LINUX
    Process.Start("xdg-open", escaped);
#else
    UnityEngine.Debug.Log($"OpenUrl unsupported platform: {Application.platform}");
#endif
}
```

When an exception occurs, a page similar to the following will open in the user's browser on Windows, macOS, and Linux.

<figure><img src="https://private-user-images.githubusercontent.com/2646053/353250070-3a3d6f82-e3bf-42bc-ae7f-582ba35cd499.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTMxMzUzOTcsIm5iZiI6MTc1MzEzNTA5NywicGF0aCI6Ii8yNjQ2MDUzLzM1MzI1MDA3MC0zYTNkNmY4Mi1lM2JmLTQyYmMtYWU3Zi01ODJiYTM1Y2Q0OTkucG5nP1gtQW16LUFsZ29yaXRobT1BV1M0LUhNQUMtU0hBMjU2JlgtQW16LUNyZWRlbnRpYWw9QUtJQVZDT0RZTFNBNTNQUUs0WkElMkYyMDI1MDcyMSUyRnVzLWVhc3QtMSUyRnMzJTJGYXdzNF9yZXF1ZXN0JlgtQW16LURhdGU9MjAyNTA3MjFUMjE1ODE3WiZYLUFtei1FeHBpcmVzPTMwMCZYLUFtei1TaWduYXR1cmU9YTc2ZjlhZWY0ZTFmMmQ3OWZmNWQzODNiMGFiODgyNTIxYTY1YTNjMDlhNTU1NWU3MTg4ODFiYjZkZDRiNGMxNSZYLUFtei1TaWduZWRIZWFkZXJzPWhvc3QifQ.UOGAVYmK7DIoJ0iwoKqiAbIqxfklQWI1cmkI3WDLbDk" alt=""><figcaption></figcaption></figure>

More information on support responses can be found [here](../../../production/setting-up-custom-support-responses.md).

### 🤖 Android

The bugsplat-unity plugin supports crash reporting for native C++ crashes on Android via Crashpad. To configure crash reporting for Android, set the `UseNativeCrashReportingForAndroid` and `UploadDebugSymbolsForAndroid` properties to `true` on the BugSplatManager instance.

You'll also need to configure the scripting backend to use IL2CPP, and target ARM64 (ARMV7a is not supported)

<figure><img src="https://private-user-images.githubusercontent.com/2646053/310610734-9ec8f5b7-8dfd-43db-84e0-7e7d1229324a.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTMxMzUzOTcsIm5iZiI6MTc1MzEzNTA5NywicGF0aCI6Ii8yNjQ2MDUzLzMxMDYxMDczNC05ZWM4ZjViNy04ZGZkLTQzZGItODRlMC03ZTdkMTIyOTMyNGEucG5nP1gtQW16LUFsZ29yaXRobT1BV1M0LUhNQUMtU0hBMjU2JlgtQW16LUNyZWRlbnRpYWw9QUtJQVZDT0RZTFNBNTNQUUs0WkElMkYyMDI1MDcyMSUyRnVzLWVhc3QtMSUyRnMzJTJGYXdzNF9yZXF1ZXN0JlgtQW16LURhdGU9MjAyNTA3MjFUMjE1ODE3WiZYLUFtei1FeHBpcmVzPTMwMCZYLUFtei1TaWduYXR1cmU9MTBhY2IyMjAzOGUzNTk1MjUwNTYxNGIyOGZkMmFmZThmOGZlNjUwMWQyMDE5OTcxNWRmZWFhNGI2YmI3NzY3OCZYLUFtei1TaWduZWRIZWFkZXJzPWhvc3QifQ.LYeu1nGJrBp3LNgpD_MISqwaVJRo61N3ViOHQ4r2fZM" alt=""><figcaption></figcaption></figure>

When you build your app for Android, be sure to set `Create symbols.zip` to `Debugging`

<figure><img src="https://private-user-images.githubusercontent.com/2646053/310611295-0181f2a8-8fb2-4745-b336-3e7f210aa55e.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTMxMzUzOTcsIm5iZiI6MTc1MzEzNTA5NywicGF0aCI6Ii8yNjQ2MDUzLzMxMDYxMTI5NS0wMTgxZjJhOC04ZmIyLTQ3NDUtYjMzNi0zZTdmMjEwYWE1NWUucG5nP1gtQW16LUFsZ29yaXRobT1BV1M0LUhNQUMtU0hBMjU2JlgtQW16LUNyZWRlbnRpYWw9QUtJQVZDT0RZTFNBNTNQUUs0WkElMkYyMDI1MDcyMSUyRnVzLWVhc3QtMSUyRnMzJTJGYXdzNF9yZXF1ZXN0JlgtQW16LURhdGU9MjAyNTA3MjFUMjE1ODE3WiZYLUFtei1FeHBpcmVzPTMwMCZYLUFtei1TaWduYXR1cmU9ZTMzNzU3NTM1ZjIwZGI4ZDFkYzJlNzJlMDQzMzVkM2ZhM2QyOGYwNjNkMWJkZTQwZmM5NDc0MzA0MzhhODk3MCZYLUFtei1TaWduZWRIZWFkZXJzPWhvc3QifQ.u3-idMBq9LcTx23tlXi5Vil89xtzQUVT2OLSrNo7PlI" alt=""><figcaption></figcaption></figure>

### 🍎 iOS

The bugsplat-unity plugin supports crash reporting for native C++ crashes on iOS via bugsplat-ios. To configure crash reporting for iOS, set the `UseNativeCrashReportingForIos` and `UploadDebugSymbolsForIos` properties to `true` on the BugSplatManager instance.

### 🧩 API

The following API methods are available to help you customize BugSplat to fit your needs.

#### BugSplatManager

| Setting                       | Description                                                                                |
| ----------------------------- | ------------------------------------------------------------------------------------------ |
| DontDestroyManagerOnSceneLoad | Should the BugSplat Manager persist through scene loads?                                   |
| RegisterLogMessageRecieved    | Register a callback function and allow BugSplat to capture instances of LogType.Exception. |

#### BugSplat Options

| Option                            | Description                                                                                                                                                                                                                                            |
| --------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Database                          | The name of your BugSplat database.                                                                                                                                                                                                                    |
| Application                       | The name of your BugSplat application. Defaults to Application.productName if no value is set.                                                                                                                                                         |
| Version                           | The version of your BugSplat application. Defaults to Application.version if no value is set.                                                                                                                                                          |
| Description                       | A default description that can be overridden by call to Post.                                                                                                                                                                                          |
| Email                             | A default email that can be overridden by call to Post.                                                                                                                                                                                                |
| Key                               | A default key that can be overridden by call to Post.                                                                                                                                                                                                  |
| Notes                             | A default general purpose field that can be overridden by call to post                                                                                                                                                                                 |
| User                              | A default user that can be overridden by call to Post                                                                                                                                                                                                  |
| CaptureEditorLog                  | Should BugSplat upload Editor.log when Post is called                                                                                                                                                                                                  |
| CapturePlayerLog                  | Should BugSplat upload Player.log when Post is called                                                                                                                                                                                                  |
| CaptureScreenshots                | Should BugSplat a screenshot and upload it when Post is called                                                                                                                                                                                         |
| PostExceptionsInEditor            | Should BugSplat upload exceptions when in editor                                                                                                                                                                                                       |
| PersistentDataFileAttachmentPaths | Paths to files (relative to Application.persistentDataPath) to upload with each report                                                                                                                                                                 |
| ShouldPostException               | Settable guard function that is called before each BugSplat report is posted                                                                                                                                                                           |
| SymbolUploadClientId              | An OAuth2 Client ID value used for uploading [symbol files](https://docs.bugsplat.com/introduction/development/working-with-symbol-files) generated via BugSplat's [Integrations](https://app.bugsplat.com/v2/settings/database/integrations) page     |
| SymbolUploadClientSecret          | An OAuth2 Client Secret value used for uploading [symbol files](https://docs.bugsplat.com/introduction/development/working-with-symbol-files) generated via BugSplat's [Integrations](https://app.bugsplat.com/v2/settings/database/integrations) page |

### 🧑‍💻 Contributing

BugSplat ❤️s open source! If you feel that this package can be improved, please open an [Issue](https://github.com/BugSplat-Git/bugsplat-unity/issues). If you have an awesome new feature you'd like to implement, we'd love to merge your [Pull Request](https://github.com/BugSplat-Git/bugsplat-unity/pulls). You can also send us an [email](mailto:support@bugsplat.com), join us on [Discord](https://discord.gg/K4KjjRV5ve), or message us via the in-app chat on [bugsplat.com](https://bugsplat.com/).
