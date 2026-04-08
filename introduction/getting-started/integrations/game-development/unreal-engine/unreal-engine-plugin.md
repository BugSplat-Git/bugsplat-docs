# Unreal Engine Plugin

{% hint style="danger" %}
The symbol-upload step in this plugin is designed for developer evaluation only! Do not release builds with Client ID and Client Secret in DefaultEngine.ini. Instead, configure an explicit [symbol-upload](https://github.com/BugSplat-Git/symbol-upload) step as part of your production build pipeline.
{% endhint %}

### 🏗 Installation

You may choose to add BugSplat through the Unreal Marketplace or add the plugin to your Unreal project manually.

#### Install from Marketplace

[Install BugSplat from the Unreal Marketplace](https://www.unrealengine.com/marketplace/en-US/product/bugsplat)

#### Install Manually

1. Navigate to your project folder containing your `[ProjectName].uproject` file.
2. Create a `Plugins` folder if it does not already exist.
3. Create a `BugSplat` folder in the `Plugins` folder and copy the contents of this repo into the `BugSplat` folder.
4. In the Unreal Editor, ensure you can access the BugSplat plugin via `Edit > Project Settings` and scroll to the `BugSplat` section under `Plugins`.

### ⚙️ Configuration

BugSplat's Unreal plugin currently supports adding crash reporting to Windows, macOS, Linux, Android, and iOS games. With a few clicks, the BugSplat plugin can be configured to automatically upload symbol files so crash report stack traces display function names and line numbers.

To get started, generate a Client ID and Client Secret via the [Integrations](https://app.bugsplat.com/v2/settings/database/integrations) page.

Next, open the BugSplat plugin menu in the Unreal Editor via `Edit > Project Settings`. Scroll to the `BugSplat` section of `Project Settings` and add values for `Database`, `Application`, `Version`, `Client ID`, and `Client Secret`:

<figure><img src="../../../../../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

#### Windows, macOS, and Linux

BugSplat leverages Unreal's `CrashReportClient` to provide crash reporting for Windows, macOS, and Linux games. Be sure to update your project settings and enable `Include Crash Reporter` and `Include Debug Files in Shipping Builds`:

<figure><img src="../../../../../.gitbook/assets/image (56).png" alt=""><figcaption></figcaption></figure>

To configure `CrashReportClient` to post to BugSplat, the `DataRouterUrl` value needs to be added to `DefaultEngine.ini`. The `bugsplat-unreal` plugin automatically updates the value for `DataRouterUrl` when the `Update Engine DefaultEngine.ini` option is enabled. Please note the `DataRouterUrl` value is global and is shared across all packaged builds created by the affected engine. To override the `DataRouterUrl` value a package build uses, you may optionally use the `Update Packaged Game INI` button under the `Tools` section.

In order to get function names and line numbers in crash reports, you'll need to upload your game's `.exe`, `.dll`, and `.pdb` files. To upload debug symbols for reach build, ensure that the `Enable Automatic Symbol Uploads` option is selected. When selected, a script to execute [symbol-upload](https://github.com/BugSplat-Git/symbol-upload) will be added to the `PostBuildSteps` field in `BugSplat.uplugin`. The symbol upload script will run automatically when your game is built.

**macOS**

To create a `.dSYM` file for a macOS build invoke `RunUAT.command` with the `-EnableDSym` flag per the example below:

```
../../../Engine/Build/BatchFiles/Mac/Build.sh -Project="../../my-unreal-crasher/MyUnrealCrasher.uproject" MyUnrealCrasher Mac Development -NoEditor -EnableDSym -TargetPath="~/Desktop/UnrealEngine"
```

#### iOS and Android

Before attempting to use the BugSplat plugin to capture crashes on Mobile, please ensure you've completed the [iOS](https://docs.unrealengine.com/5.0/en-US/setting-up-an-unreal-engine-project-for-ios/) and [Android](https://docs.unrealengine.com/5.0/en-US/android-support-for-unreal-engine/) quickstart guides.

To enable crash reporting, ensure the `Enable iOS Crash Reporting` and `Enable Android Crash Reporting` options are selected. Also, ensure that `Enable Automatic Symbol Uploads` is checked so that your crash reports contain function names and line numbers.

**iOS**

{% hint style="warning" %}
The Unreal iOS project's build process includes a `Build Phase called Generate dSYM for archive, and strip`, which executes after the Unreal PostBuildSteps. However, this Build Phase must complete before the `dSYM` file (debug symbols) is generated. Due to this timing, BugSplat cannot upload the `dSYM` immediately during the initial build. Instead, BugSplat will upload the `dSYM` during the next incremental build in Xcode. Alternatively, you can follow the [example](https://github.com/BugSplat-Git/bugsplat-apple/blob/main/Symbol_Upload_Examples/Build-Phase-symbol-upload.sh) in our bugsplat-apple repo to configure a custom Build Phase for symbol uploads.
{% endhint %}

To get function names and line numbers in your iOS crash reports, please make the following changes in the `iOS` section of `Project Settings`.

| Option                                                 | Value |
| ------------------------------------------------------ | ----- |
| Generate dSYMs for code debugging and profiling        | true  |
| Generate dSYMs as a bundle for third-party crash tools | true  |
| Support bitcode in shipping                            | false |

Note that sometimes iOS applications won't crash while the USB cable is connected. If this happens, disconnect the USB cable and re-run the application to trigger a crash.

**Android**

{% hint style="warning" %}
Code is aggressively optimized when building for Android. Oftentimes, Unreal's build process optimizes away code that generates simple errors used in testing. To test a null pointer dereference, you can add the `volatile` keyword to work around compiler optimizations.
{% endhint %}

Fatal Errors on Android raise a `SIGTRAP` and require extra configuration so that they can be reported to BugSplat.

<details>

<summary>Click to reveal Android Error Output Device code</summary>

[MyUnrealCrasherErrorOutputDevice.h](https://github.com/BugSplat-Git/my-unreal-crasher/blob/b0a805505a661d6729657bcae724e64dea31484b/Source/MyUnrealCrasher/MyUnrealCrasherAndroidErrorOutputDevice.h)

```cpp
#pragma once

#include "CoreMinimal.h"
#include "Misc/OutputDeviceError.h"

#if PLATFORM_ANDROID
class FMyUnrealCrasherAndroidErrorOutputDevice : public FOutputDeviceError
{
public:
	virtual ~FMyUnrealCrasherAndroidErrorOutputDevice() {}
	
	virtual void Serialize(const TCHAR* V, ELogVerbosity::Type Verbosity, const FName& Category) override;
	virtual void HandleError() override;
	
	static FOutputDeviceError* GetErrorOutputDevice();
	
private:
	void RequestExit(bool Force, const TCHAR* CallSite);
};
#endif
```

[MyUnrealCrasherErrorOutputDevice.cpp](https://github.com/BugSplat-Git/my-unreal-crasher/blob/b0a805505a661d6729657bcae724e64dea31484b/Source/MyUnrealCrasher/MyUnrealCrasherAndroidErrorOutputDevice.cpp)

```cpp
#include "MyUnrealCrasherAndroidErrorOutputDevice.h"

#include "CoreMinimal.h"
#include "CoreGlobals.h"
#include "Misc/OutputDevice.h"
#include "Misc/OutputDeviceHelper.h"
#include "Misc/App.h"
#include "Misc/CoreDelegates.h"
#include "Misc/FeedbackContext.h"
#include "HAL/PlatformMisc.h"
#include "HAL/PlatformCrt.h"

#if PLATFORM_ANDROID
#include "Android/AndroidPlatform.h" // For LogAndroid

FOutputDeviceError* FMyUnrealCrasherAndroidErrorOutputDevice::GetErrorOutputDevice()
{
	static FMyUnrealCrasherAndroidErrorOutputDevice ErrorOutputDevice;
	return &ErrorOutputDevice;
}

void FMyUnrealCrasherAndroidErrorOutputDevice::Serialize( const TCHAR* Msg, ELogVerbosity::Type Verbosity, const class FName& Category )
{
	FPlatformMisc::LowLevelOutputDebugString(*FOutputDeviceHelper::FormatLogLine(Verbosity, Category, Msg, GPrintLogTimes));

	static int32 CallCount = 0;
	int32 NewCallCount = FPlatformAtomics::InterlockedIncrement(&CallCount);
	if(GIsCriticalError == 0 && NewCallCount == 1)
	{
		// First appError.
		GIsCriticalError = 1;

		FCString::Strncpy(GErrorExceptionDescription, Msg, UE_ARRAY_COUNT(GErrorExceptionDescription));
	}
	else
	{
		UE_LOG(LogAndroid, Error, TEXT("Error reentered: %s"), Msg);
	}

	if (GIsGuarded)
	{
		UE_DEBUG_BREAK();
	}
	else
	{
		HandleError();
		RequestExit(true, TEXT("MyUnrealCrasherAndroidErrorOutputDevice::Serialize.!GIsGuarded"));
	}
}

void FMyUnrealCrasherAndroidErrorOutputDevice::HandleError()
{
	static int32 CallCount = 0;
	int32 NewCallCount = FPlatformAtomics::InterlockedIncrement(&CallCount);

	if (NewCallCount != 1)
	{
		UE_LOG(LogAndroid, Error, TEXT("HandleError re-entered."));
		return;
	}
	
	GIsGuarded = 0;
	GIsRunning = 0;
	GIsCriticalError = 1;
	GLogConsole = NULL;
	GErrorHist[UE_ARRAY_COUNT(GErrorHist) - 1] = 0;

	// Dump the error and flush the log.
#if !NO_LOGGING
	FDebug::LogFormattedMessageWithCallstack(LogAndroid.GetCategoryName(), __FILE__, __LINE__, TEXT("=== Critical error: ==="), GErrorHist, ELogVerbosity::Error);
#endif
	
	GLog->Panic();

	FCoreDelegates::OnHandleSystemError.Broadcast();
	FCoreDelegates::OnShutdownAfterError.Broadcast();
}


void FMyUnrealCrasherAndroidErrorOutputDevice::RequestExit( bool Force, const TCHAR* CallSite)
{

#if PLATFORM_COMPILER_OPTIMIZATION_PG_PROFILING
	// Write the PGO profiling file on a clean shutdown.
	extern void PGO_WriteFile();
	if (!GIsCriticalError)
	{
		PGO_WriteFile();
		// exit now to avoid a possible second PGO write when AndroidMain exits.
		Force = true;
	}
#endif

	UE_LOG(LogAndroid, Log, TEXT("FMyUnrealCrasherAndroidErrorOutputDevice::RequestExit(%i, %s)"), Force,
		CallSite ? CallSite : TEXT("<NoCallSiteInfo>"));
	if (GLog)
	{
		GLog->Flush();
	}

	if (Force)
	{
		abort(); // Abort to trigger a crash report
	}
	else
	{
		RequestEngineExit(TEXT("Android RequestExitWithCrashReporting")); // Called regardless in our version to set up the crash context
	}
}
#endif
```

[MyUnrealCrasherGameInstance.h](https://github.com/BugSplat-Git/my-unreal-crasher/blob/b0a805505a661d6729657bcae724e64dea31484b/Source/MyUnrealCrasher/MyUnrealCrasherGameInstance.h)

```cpp
#pragma once

#include "CoreMinimal.h"
#include "Engine/GameInstance.h"
#include "MyUnrealCrasherGameInstance.generated.h"

UCLASS()
class MYUNREALCRASHER_API UMyUnrealCrasherGameInstance : public UGameInstance
{
	GENERATED_BODY()

public:
	virtual void Init() override;
};
```

[MyUnrealCrasherGameInstance.cpp](https://github.com/BugSplat-Git/my-unreal-crasher/blob/b0a805505a661d6729657bcae724e64dea31484b/Source/MyUnrealCrasher/MyUnrealCrasherGameInstance.cpp)

```cpp
//  Copyright © BugSplat. All rights reserved.
#include "MyUnrealCrasherGameInstance.h"
#include "MyUnrealCrasherAndroidErrorOutputDevice.h"

#if PLATFORM_ANDROID
#include <android/log.h>
#endif

void UMyUnrealCrasherGameInstance::Init()
{
	Super::Init();
	UE_LOG(LogTemp, Log, TEXT("MyUnrealCrasherGameInstance::Init - Setting custom error output device"));

#if PLATFORM_ANDROID
	GError = FMyUnrealCrasherAndroidErrorOutputDevice::GetErrorOutputDevice();
#endif
}
```

</details>

**Setting Attributes (iOS and Android)**

The BugSplat plugin automatically adds several attributes from the crash context at initialization time. These attributes are not updated automatically after initialization. If you'd like to update them or add new ones, use the `SetAttribute` and `RemoveAttribute` functions.

To call `SetAttribute` from C++, add `BugSplatRuntime` to your module's `PrivateDependencyModuleNames` in your `.Build.cs` file:

```csharp
PrivateDependencyModuleNames.AddRange(new string[] { "BugSplatRuntime" });
```

Then include the header and call `SetAttribute`:

```cpp
#include "BugSplatAttributes.h"

UBugSplatAttributes::SetAttribute(TEXT("userId"), TEXT("12345"));
```

`SetAttribute` is also available as a Blueprint node under the BugSplat category.

#### Xbox and PlayStation

BugSplat can provide instructions for implementing Unreal crash reporting on Xbox and PlayStation. Please email us at [support@bugsplat.com](mailto:support@bugsplat.com) for more info.

### 🏃 Usage

Once you've installed the plugin, add the following C++ snippet to your game to generate a sample crash.

```cpp
UE_LOG(LogTemp, Fatal, TEXT("BugSplat!"));
```

Run your application and submit a crash report.

On Desktops, submit a crash report via the Unreal CrashReportClient dialog that appears at crash time. We have developed a handy guide on how you can customize the Unreal CrashReportClient dialog that is available [here](https://www.bugsplat.com/blog/game-dev/customizing-unreal-engine-crash-dialog/).

On iOS, after a crash occurs, restart the game and tap the `Send Report` option when prompted. On Android, crashes are submitted automatically at crash time.

Once you've submitted a crash report, navigate to the [Crashes](https://app.bugsplat.com/v2/crashes) page. On the Crashes page, click the link in the ID column.

If everything is configured correctly, you should see something that resembles the following:

<figure><img src="../../../../../.gitbook/assets/image (57).png" alt=""><figcaption></figcaption></figure>

### 💬 User Feedback

In addition to crash reporting, BugSplat supports collecting non-crashing user feedback such as bug reports and feature requests. Feedback reports appear in BugSplat with the "User Feedback" type, grouped by title.

The BugSplat Unreal plugin provides two ways to collect user feedback:

#### Built-in Feedback Dialog

The plugin includes a ready-to-use feedback dialog. To add it to your game:

1. Open any Widget Blueprint (e.g., your HUD or main menu).
2. Add a **Button** widget and position it where you'd like the feedback trigger to appear.
3. In the button's **Events** section, click the **+** next to **On Clicked**.
4. In the Event Graph, search for **Show Feedback Dialog** (under the BugSplat category) and connect it to the On Clicked event.

The dialog includes a subject field, optional description, and an "Include application logs" checkbox that attaches the Unreal Engine log file. Crash context attributes (engine version, platform, CPU, GPU, memory, OS) are included automatically with every submission.

#### Custom Feedback UI

If you'd prefer to build your own feedback UI, call `UBugSplatFeedback::PostFeedback` directly. It reads Database, Application, and Version from your plugin settings and handles the HTTP submission, crash context attributes, and file attachments.

#### C++

```cpp
#include "BugSplatFeedback.h"

// Simple feedback
UBugSplatFeedback::PostFeedback(
    TEXT("Login button broken"),            // Title (required)
    TEXT("Nothing happens when I tap it"),  // Description
    TArray<FString>()                       // Attachments
);
```

To include file attachments, user info, and custom attributes:

```cpp
#include "BugSplatFeedback.h"

TArray<FString> Attachments;
Attachments.Add(UBugSplatFeedback::GetLogFilePath());
Attachments.Add(FPaths::ProjectSavedDir() / TEXT("screenshot.png"));

TMap<FString, FString> CustomAttributes;
CustomAttributes.Add(TEXT("Level"), TEXT("Tutorial_03"));
CustomAttributes.Add(TEXT("PlaytimeMinutes"), TEXT("42"));

UBugSplatFeedback::PostFeedback(
    TEXT("Login button broken"),        // Title (required)
    TEXT("Nothing happens when I tap"), // Description
    Attachments,                        // File paths
    TEXT("Jane"),                        // User
    TEXT("jane@example.com"),           // Email
    TEXT(""),                            // AppKey
    CustomAttributes                    // Merged with crash context attributes
);
```

The full `PostFeedback` signature:

| Parameter          | Required | Description                                           |
| ------------------ | -------- | ----------------------------------------------------- |
| `Title`            | Yes      | Brief summary of the feedback (becomes the stack key) |
| `Description`      | No       | Additional details                                    |
| `Attachments`      | No       | Array of absolute file paths to attach                |
| `User`             | No       | Username of the person submitting                     |
| `Email`            | No       | Email of the person submitting                        |
| `AppKey`           | No       | Application key                                       |
| `CustomAttributes` | No       | Key-value pairs merged with crash context attributes  |

#### Handling completion (C++)

For C++ callers that need to handle success or failure (e.g., to update your own UI), use `PostFeedbackWithCallback`:

```cpp
UBugSplatFeedback::PostFeedbackWithCallback(
    TEXT("My title"), TEXT("Details"), Attachments,
    TEXT(""), TEXT(""), TEXT(""), TMap<FString, FString>(),
    FBugSplatFeedbackComplete::CreateLambda([](bool bSuccess, int32 HttpStatusCode)
    {
        if (bSuccess) { /* show success */ }
        else { /* show error */ }
    })
);
```

#### Blueprints

Call the **Post Feedback** node from `BugSplatFeedback`. The advanced parameters (User, Email, AppKey, CustomAttributes) are collapsed by default — expand the node to access them. Use the **Get Log File Path** node to get the path to the current Unreal Engine log file.

For the full API, see [`BugSplatFeedback.h`](https://github.com/BugSplat-Git/bugsplat-unreal/blob/main/Source/BugSplatRuntime/Public/BugSplatFeedback.h). The built-in dialog in [`BugSplatFeedbackDialog.cpp`](https://github.com/BugSplat-Git/bugsplat-unreal/blob/main/Source/BugSplatRuntime/Private/BugSplatFeedbackDialog.cpp) serves as a reference implementation.

### 🧑‍💻 Contributing

BugSplat ❤️s open source! If you feel that this package can be improved, please open an [Issue](https://github.com/BugSplat-Git/bugsplat-unreal/issues). If you have an awesome new feature you'd like to implement, we'd love to merge your [Pull Request](https://github.com/BugSplat-Git/bugsplat-unreal/pulls). You can also send us an [email](mailto:support@bugsplat.com), join us on [Discord](https://discord.gg/K4KjjRV5ve), or message us via the in-app chat on [bugsplat.com](https://bugsplat.com/).
