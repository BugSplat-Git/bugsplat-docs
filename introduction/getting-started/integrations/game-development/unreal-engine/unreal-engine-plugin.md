# Unreal Engine Plugin

{% hint style="info" %}
The [bugsplat-unreal](https://github.com/BugSplat-Git/bugsplat-unreal) plugin is currently in beta.
{% endhint %}

BugSplat's Unreal Engine plugin currently supports adding crash reporting to games running on Windows, macOS, Linux, iOS, and Android. With a few clicks, the BugSplat plugin can be configured to automatically upload symbol files so crash report stack traces display function names and line numbers.

To get started with BugSplat's Unreal Engine plugin please check out our [GitHub](https://github.com/BugSplat-Git/bugsplat-unreal) repo. Additionally, an example game, configured with BugSplat's Unreal Engine plugin can be found [here](https://github.com/BugSplat-Git/my-unreal-crasher).

### Install from GitHub

1. Navigate to your project folder, which contains your `[ProjectName].uproject` file.
2. If it does not already exist, create a `Plugins` folder.
3. Create a `BugSplat` folder in the `Plugins` folder and copy the contents of the [bugsplat-unreal](https://github.com/BugSplat-Git/bugsplat-unreal) repo into the `BugSplat` folder.
4. In the Unreal Editor, ensure you can access the BugSplat plugin via `Edit > Project Settings` and scrolling to the `BugSplat` section under `Plugins`.

### Configuration

To get started, generate a Client ID and Client Secret via the [Integrations](https://app.bugsplat.com/v2/settings/database/integrations) page.

Next, open the BugSplat plugin menu in the Unreal Editor via `Edit > Project Settings`. Scroll to the `BugSplat` section of `Project Settings` and add values for `Database`, `Application`, `Version`, `Client ID`, and `Client Secret`:

<figure><img src="https://github.com/BugSplat-Git/bugsplat-unreal/raw/main/.assets/bugsplat-project-settings.png" alt=""><figcaption><p>Plugin Project Settings</p></figcaption></figure>

#### Desktop

For Desktop, the BugSplat plugin has the ability to modify the [DefaultEngine.ini](https://docs.unrealengine.com/5.0/en-US/configuration-files-in-unreal-engine/) file for both a packaged build or the global engine so that crashes are posted to BugSplat. Additionally, the BugSplat plugin can add a [PostBuildStep](https://docs.unrealengine.com/5.0/en-US/unreal-engine-build-tool-target-reference/) that will upload Windows [symbol files](https://docs.bugsplat.com/introduction/development/working-with-symbol-files) to BugSplat after each build.

BugSplat recommends configuring the crash reporting independently for each packaged build. To configure crash reporting in a packaged build select `Update Game INI`. When prompted, navigate to the root directory of your packaged build that contains the folder `Windows` or `WindowsNoEditor`. Note that you will need to **repeat this step and update the version information every time you create a packaged version of your game**. For production scenarios consider automating this step with a script on your build machine.

<figure><img src="https://github.com/BugSplat-Git/bugsplat-unreal/raw/main/.assets/packaged-directory.png" alt=""><figcaption></figcaption></figure>

Alternatively, BugSplat can be configured to collect crash reports in the editor and all games built with the current engine. To configure BugSplat for the current engine, select `Update Global INI`. Note that updating the global `DefaultEngine.ini` file **will affect all projects using the same engine build**.

In order to get function names and line numbers in crash reports you'll need to upload your game's `.exe`, `.dll`, and `.pdb` files. To upload symbol files to BugSplat, select the `Add Symbol Uploads` button. The `Add Symbol Uploads` button will generate a bash script which uploads your project's [symbol files](https://docs.bugsplat.com/introduction/development/working-with-symbol-files) to BugSplat. A script to execute [SendPdbs.exe](https://docs.bugsplat.com/education/faq/using-sendpdbs-to-automatically-upload-symbol-files) will be added to the `PostBuildSteps` field in `BugSplat.uplugin` and will run automatically when your game is built.

#### Mobile

Select the `Add symbol uploads` and `Add crash reporting` checkboxes in the `Mobile` section of the plugin dialog. Once enabled, the BugSplat plugin will configure crash reporting and symbol uploads automatically on mobile platforms.

Before attempting to use the BugSplat plugin to capture crashes on Mobile please ensure you've completed the [iOS](https://docs.unrealengine.com/5.0/en-US/setting-up-an-unreal-engine-project-for-ios/) and [Android](https://docs.unrealengine.com/5.0/en-US/android-support-for-unreal-engine/) quickstart guides.

In order to get function names and line numbers in your iOS crash reports, please make the following changes in the `iOS` section of `Project Settings`

| Option                                                 | Value |
| ------------------------------------------------------ | ----- |
| Generate dSYMs for code debugging and profiling        | true  |
| Generate dSYMs as a bundle for third party crash tools | true  |
| Support bitcode in shipping                            | false |

Note that sometimes iOS applications won't crash while the USB cable is connected. If this happens, disconnect the USB cable and re-run the application to trigger a crash.

### Usage

Once you've installed the plugin, add the following C++ snippet to your game to generate a sample crash.

```
UE_LOG(LogTemp, Fatal, TEXT("BugSplat!"));
```

Run your application and submit a crash report.

On Desktops, submit a crash report via the Unreal CrashReportClient dialog that appears at crash time. We have developed a handy guide on how you can customize the Unreal CrashReportClient dialog that is available [here](https://www.bugsplat.com/blog/game-dev/customizing-unreal-engine-crash-dialog/).

On iOS, after a crash occurs, restart the game and tap the `Send Report` option when prompted.

Once you've submitted a crash report navigate to the [Crashes](https://app.bugsplat.com/v2/crashes) page. On the Crashes page, click the link in the ID column.

If everything is configured correctly, you should see something that resembles the following:

<figure><img src="https://github.com/BugSplat-Git/bugsplat-unreal/raw/main/.assets/unreal-crash.png" alt=""><figcaption></figcaption></figure>

### Contributing

BugSplat ❤️s open source! If you feel that this package can be improved, please open an [Issue](https://github.com/BugSplat-Git/bugsplat-unreal/issues). If you have an awesome new feature you'd like to implement, we'd love to merge your [Pull Request](https://github.com/BugSplat-Git/bugsplat-unreal/pulls). You can also send us an [email](mailto:support@bugsplat.com), join us on [Discord](https://discord.gg/K4KjjRV5ve), or message us via the in-app chat on [bugsplat.com](https://bugsplat.com/).
