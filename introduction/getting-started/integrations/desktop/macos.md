# macOS

### Introduction 👋

BugSplat.xcframework enables posting crash reports from iOS, macOS, and Mac Catalyst applications to BugSplat. Visit [bugsplat.com](https://www.bugsplat.com/) for more information and to sign up for an account.

### Requirements 📋

* BugSplat for iOS supports iOS 13 and later.
* BugSplat for macOS supports macOS 10.13 and later.

### Integration 🏗️

BugSplat supports multiple methods for installing the xcfamework in a project.

#### Swift Package Manager (SPM)

Add the following URL to your project's `Additional Package Dependencies`:

```
https://github.com/BugSplat-Git/bugsplat-apple
```

#### Manual Setup

To manually install BugSplat.xcframework in your project:

1. Download the latest release from the [Releases](https://github.com/BugSPlat-Git/bugsplat-apple/releases) page. The release will contain a zip file with the xcframework.
2. Unzip the archive.
3. In Xcode, select your app target, then go to the General tab, scroll down to Framework, Libraries, and Embedded Content, then click the "+" and navigate to locate the unzipped BugSplat.xcframework. Once added, select Embed & Sign.

### Usage 🧑‍💻

#### Configuration

BugSplat requires a few Xcode configuration steps to integrate the xcframework with your BugSplat account.

Add the following case-sensitive key to your app's `Info.plist` replacing `DATABASE_NAME` with your customer-specific BugSplat database name.

```xml
<key>BugSplatDatabase</key>
<string>DATABASE_NAME</string>
```

{% hint style="info" %}
For macOS apps, you must enable `Outgoing network connections (client)` in the Signing & Capabilities of the Target.
{% endhint %}

#### Symbol Upload

To symbolicate crash reports, you must upload your app's `dSYM` files to the BugSplat server. There are scripts to help with this.

Download BugSplat's cross-platform tool, [symbol-upload-macos](https://docs.bugsplat.com/education/faq/how-to-upload-symbol-files-with-symbol-upload) for Apple Silicon by entering the following command in your terminal.

```bash
curl -sL -O "https://app.bugsplat.com/download/symbol-upload-macos"
```

Alternatively, you can download the Intel version via the following command.

```bash
curl -sL -O "https://app.bugsplat.com/download/symbol-upload-macos-intel"
```

Make `symbol-upload-macos` executable

```bash
chmod +x symbol-upload-macos
```

Several options exist to integrate `symbol-upload-macos` into the app build process.

* Create an Xcode build-phase script to upload dSYM files after every build. See example script [Symbol\_Upload\_Examples/Build-Phase-symbol-upload.sh](https://github.com/BugSplat-Git/bugsplat-apple/blob/main/Symbol_Upload_Examples/Build-Phase-symbol-upload.sh)
* Create an Xcode Archive post-action script in the target's Build Scheme in order to upload dSYM files after the app is archived and ready for submission to TestFlight or the App Store. See example script [Symbol\_Upload\_Examples/Archive-post-action-upload.sh](https://github.com/BugSplat-Git/bugsplat-apple/blob/main/Symbol_Upload_Examples/Archive-post-action-upload.sh)
* Manually upload an `xcarchive` or `dSYM` file generated by Xcode via BugSplat's [Versions](https://app.bugsplat.com/v2/versions) page.

{% hint style="info" %}
For the build-phase script to create dSYM files, change Build Settings `DEBUG_INFORMATION_FORMAT` from `DWARF` to `DWARF with dSYM File`. See inline notes within each script for modifications to Xcode Build Settings required for each script to work.
{% endhint %}

Please refer to our [documentation](https://docs.bugsplat.com/education/faq/how-to-upload-symbol-files-with-symbol-upload) to learn more about how to use `symbol-upload-macos`.

#### Initialization

Several iOS and macOS test app examples are included within the [Example\_Apps](https://github.com/BugSplat-Git/bugsplat-apple/blob/main/Example_Apps) folder to show how simple and quickly BugSplat can be integrated into an app, and ready to submit crash reports.

You can instantiate BugSplat by following the language-specific examples below.

**Swift (UIKit)**

```swift
import BugSplat

@main
class AppDelegate: UIResponder, UIApplicationDelegate {
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        // Initialize BugSplat
        BugSplat.shared().delegate = self
        BugSplat.shared().autoSubmitCrashReport = false
        BugSplat.shared().start()

        return true
    }
}

extension AppDelegate: BugSplatDelegate {
    // MARK: BugSplatDelegate
    func bugSplatWillSendCrashReport(_ bugSplat: BugSplat) {
        print("\(#file) - \(#function)")
    }

    func bugSplatWillSendCrashReportsAlways(_ bugSplat: BugSplat) {
        print("\(#file) - \(#function)")
    }

    func bugSplatDidFinishSendingCrashReport(_ bugSplat: BugSplat) {
        print("\(#file) - \(#function)")
    }

    func bugSplatWillCancelSendingCrashReport(_ bugSplat: BugSplat) {
        print("\(#file) - \(#function)")
    }

    func bugSplatWillShowSubmitCrashReportAlert(_ bugSplat: BugSplat) {
        print("\(#file) - \(#function)")
    }

    func bugSplat(_ bugSplat: BugSplat, didFailWithError error: Error) {
        print("\(#file) - \(#function)")
    }
}
```

**Swift (SwiftUI)**

```swift
import BugSplat

@main
struct BugSplatTestSwiftUIApp: App {
    private let bugSplat = BugSplatInitializer()

    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}

@objc class BugSplatInitializer: NSObject, BugSplatDelegate {
    override init() {
        super.init()
        BugSplat.shared().delegate = self
        BugSplat.shared().autoSubmitCrashReport = false
        BugSplat.shared().start()
    }

    // MARK: BugSplatDelegate
    func bugSplatWillSendCrashReport(_ bugSplat: BugSplat) {
        print("\(#file) - \(#function)")
    }

    func bugSplatWillSendCrashReportsAlways(_ bugSplat: BugSplat) {
        print("\(#file) - \(#function)")
    }

    func bugSplatDidFinishSendingCrashReport(_ bugSplat: BugSplat) {
        print("\(#file) - \(#function)")
    }

    func bugSplatWillCancelSendingCrashReport(_ bugSplat: BugSplat) {
        print("\(#file) - \(#function)")
    }

    func bugSplatWillShowSubmitCrashReportAlert(_ bugSplat: BugSplat) {
        print("\(#file) - \(#function)")
    }

    func bugSplat(_ bugSplat: BugSplat, didFailWithError error: Error) {
        print("\(#file) - \(#function)")
    }
}
```

**Obj-C**

```objectivec
#import "BugSplatMac/BugSplatMac.h"

@interface AppDelegate () <BugSplatDelegate>
@end

@implementation AppDelegate
- (void)applicationDidFinishLaunching:(NSNotification *)aNotification {
    // Initialize BugSplat
    [[BugSplat shared] setDelegate:self];
    [[BugSplat shared] setAutoSubmitCrashReport:NO];
    [[BugSplat shared] start];
}

#pragma mark - BugSplatDelegate

- (void)bugSplatWillSendCrashReport:(BugSplat *)bugSplat {
    NSLog(@"bugSplatWillSendCrashReport called");
}

- (void)bugSplatWillSendCrashReportsAlways:(BugSplat *)bugSplat {
    NSLog(@"bugSplatWillSendCrashReportsAlways called");
}

- (void)bugSplatDidFinishSendingCrashReport:(BugSplat *)bugSplat {
    NSLog(@"bugSplatDidFinishSendingCrashReport called");
}

- (void)bugSplatWillCancelSendingCrashReport:(BugSplat *)bugSplat {
    NSLog(@"bugSplatWillCancelSendingCrashReport called");
}

- (void)bugSplatWillShowSubmitCrashReportAlert:(BugSplat *)bugSplat {
    NSLog(@"bugSplatWillShowSubmitCrashReportAlert called");
}

- (void)bugSplat:(BugSplat *)bugSplat didFailWithError:(NSError *)error {
    NSLog(@"bugSplat:didFailWithError: %@", [error localizedDescription]);
}
```

#### Attributes

BugSplat supports custom attributes that can be added to a crash report. These attributes are searchable in the BugSplat dashboard.

```swift
BugSplat.shared().setValue("Value of Attribute", forAttribute: "AttributeName")
```

```objectivec
[[BugSplat shared] setValue:@"Value of Attribute" forAttribute:@"AttributeName"];
```

Please see the framework-specific [sample applications](macos.md#sample-applications) for more examples demonstrating how to use attributes.

#### Crash Reporter Customization

There are several ways to customize your BugSplat crash reporter.

**Custom Banner Image**

* BugSplat for macOS provides the ability to configure a custom image to be displayed in the crash reporter UI for branding purposes. The image view dimensions are 440x110 and will scale down proportionately. There are 2 ways developers can provide an image:
  1. Set the image property directly on BugSplat
  2. Provide an image named `bugsplat-logo` in the main app bundle or asset catalog

**User Details**

* Set `askUserDetails` to `NO` to prevent the name and email fields from displaying in the crash reporter UI. Defaults to `YES`.

**Auto Submit**

* By default, BugSplat will auto-submit crash reports for iOS and prompt the end user to submit a crash report for macOS. This default can be changed using a BugSplat property autoSubmitCrashReport. Set `autoSubmitCrashReport` to `YES` in order to send crash reports to the server automatically without presenting the crash reporter dialogue.

**Persist User Details**

* Set `persistUserDetails` to `YES` to save and restore the user's name and email when presenting the crash reporter dialogue. Defaults to `NO`.

**Expiration Time**

* Set `expirationTimeInterval` to a desired value (in seconds) whereby if the difference in time between when the crash occurred and the next launch is greater than the set expiration time, auto-send the report without presenting the crash reporter dialogue. Defaults to `-1`, which represents no expiration.

**Attachments**

Bugsplat supports uploading attachments with crash reports. There's a delegate method provided by `BugSplatDelegate` that can be implemented to provide attachments to be uploaded.

**Bitcode**

Bitcode was introduced by Apple to allow apps sent to the App Store to be recompiled by Apple itself and apply the latest optimization. Bitcode has now been officially deprecated by Apple and should be removed or disabled. If Bitcode is enabled, the symbols generated for your app in the store will be different than the ones from your own build system. We recommend that you disable bitcode in order for BugSplat to reliably symbolicate crash reports. Disabling bitcode significantly simplifies symbols management and currently doesn't have any known downsides for iOS apps.

**Localization**

For macOS, the BugSplat crash dialogue can be localized and supports 8 languages out of the box.

1. English
2. Finnish
3. French
4. German
5. Italian
6. Japanese
7. Norwegian
8. Swedish

Additional languages may be supported by adding the language bundle and strings file to `BugSplat.xcframework/macos-arm64_x86_64/BugSplatMac.framework/Versions/A/Frameworks/HockeySDK.framework/Resources/`

### Sample Applications 🧑‍🏫

`Example_Apps` includes several iOS and macOS BugSplat Test apps. Integrating BugSpat only requires the xcframework, and a few lines of code.

1. Clone the [bugsplat-apple repo](https://github.com/BugSplat-Git/bugsplat-apple).
2. Open an example Xcode project from `Example_Apps`. For iOS, set the destination to be your iOS device. After running from Xcode, stop the process and relaunch from the iOS device directly.
3. Once the app launches, click the "crash" button when prompted.
4. Relaunch the app on the iOS device. At this point a crash report should be submitted to bugsplat.com
5. Visit BugSplat's [Crashes](https://app.bugsplat.com/v2/crashes) page. When prompted for credentials, enter user `fred@bugsplat.com` and password `Flintstone`. The crash you posted from BugSplatTester should be at the top of the list of crashes.
6. Click the "Crash ID" link to view more details about your crash.
