# macOS

## Introduction

Before integrating a new BugSplat SDK with your application, make sure to review the [Getting Started](https://www.bugsplat.com/resources/bugsplat-101/) resources and complete the simple startup tasks listed below.

* [Sign up](https://app.bugsplat.com/v2/sign-up) for a BugSplat account
* [Log in](https://app.bugsplat.com/auth0/login) using your email address
* Create a new [database](https://app.bugsplat.com/v2/company) for your application

Need any further help? Check out the full BugSplat documentation [here](https://www.bugsplat.com/docs), or email the team at [support@bugsplat.com](mailto:support@bugsplat.com).

## Overview

The BugsplatMac OS X framework enables posting crash reports from Cocoa applications to BugSplat. This allows for your team to process OS X crash reports with BugSplat.

## Requirements

* BugsplatMac supports OS X 10.7 and later.
* BugsplatMac supports x86\_64 and Apple Silicon applications

## Integration

BugsplatMac supports multiple methods for installing the library in a project.

### Installation with CocoaPods

CocoaPods is a dependency manager for Objective-C, which automates and simplifies the process of using 3rd-party libraries like BugsplatMac in your projects. You can install it with the following command:

```text
$ gem install cocoapods
```

### Podfile

To integrate BugsplatMac into your Xcode project using CocoaPods, specify it in your `Podfile`:

```text
$ pod install
```

The pod install command creates an xcworkspace file next to your application's xcodeproj file. Open the xcworkspace file in lieu of the xcodeproj file to ensure BugsplatMac.framework is included in your build.

### **Installation with Carthage**

Carthage is a decentralized dependency manager that builds your dependencies and provides you with binary frameworks.

You can install Carthage with Homebrew using the following command:

```text
$ brew update
$ brew install carthage
```

To integrate BugsplatMac into your Xcode project using Carthage, specify it in your `Cartfile`:

```text
github "BugsplatGit/BugsplatMac"
```



Run `carthage` to build the framework and drag the built `BugsplatMac.framework` into your Xcode project.

### Manual Setup

To use this library in your project manually you may:

1. Download the latest release from [https://github.com/BugSplatGit/BugsplatMac/releases](https://github.com/BugSplatGit/BugsplatMac/releases) which is provided as a zip file
2. Unzip the archive and add BugsplatMac.framework to your Xcode project
3. Drag & drop `BugsplatMac.framework` from your window in the `Finder` into your project in Xcode and move it to the desired location in the `Project Navigator`
4. A popup will appear. Select `Create groups for any added folders` and set the checkmark for your target. Then click `Finish`.
5. Configure the framework to be copied into your app bundle:

   * Click on your project in the `Project Navigator` \(⌘+1\).
   * Click your target in the project editor.
   * Click on the `Build Phases` tab.
   * Click the `Add Build Phase` button at the bottom and choose `Add Copy Files`.
   * Click the disclosure triangle next to the new build phase.
   * Choose `Frameworks` from the Destination list.
   * Drag `BugsplatMac` from the Project Navigator left sidebar to the list in the new Copy Files phase.

## Usage

### Configuration

BugsplatMac requires a few configuration steps in order to integrate the framework with your Bugsplat account

#### Step 1

Add the following key to your app's Info.plist replacing DATABASE\_NAME with your account name

```text
<key>BugsplatServerURL</key>
<string>https://DATABASE_NAME.bugsplat.com/</string>
```

#### Step 2

You must upload an xcarchive containing your app's binary and symbols to the Bugsplat server in order to symbolicate crash reports.

Create a ~/.bugsplat.conf file to store your Bugsplat credentials

```text
BUGSPLAT_USER="<username>"
BUGSPLAT_PASS="<password>"
```

Add the upload-archive.sh script located in Bugsplat.framework/Versions/A/Resources as an Archive post-action in your build scheme. The script will be invoked when archiving completes which will upload the xcarchive to Bugsplat for processing. You can view the script output in `/tmp/bugsplat-upload.log`. To share amongst your team, mark the scheme as 'Shared'. 

![Integrating OS X with BugSplat](https://www.bugsplat.com/assets/img/docs/post-archive-script.png)

### Initialization

```text
@import BugsplatMac;
```

```text
- (void)applicationDidFinishLaunching:(NSNotification *)aNotification
{
  [[BugsplatStartupManager sharedManager] start];                          
}
```



### Crash Reporter UI Customization

#### Step 1

Custom banner image

* Bugsplat provides the ability to configure a custom image to be displayed in the crash reporter UI for branding purposes. The image view dimensions are 440x110 and will scale down proportionately. There are 2 ways developers can provide an image:
  1. Set the image property directly on BugsplatStartupManager
  2. Provide an image named `bugsplat-logo` in the main app bundle or asset catalog

#### User details

* Set `askUserDetails` to `NO` in order to prevent the name and email fields from displaying in the crash reporter UI

### Attachments

Bugsplat supports uploading attachments with crash reports. There's a delegate method provided by `BugsplatStartupManagerDelegate` that can be implemented to provide an attachment to be uploaded.

```text
- (NSArray<BugsplatAttachment *> *)attachmentsForBugsplatStartupManager:(BugsplatStartupManager *)bugsplatStartupManager
{
    NSURL *fileURL = [[NSBundle mainBundle] URLForResource:@"generated" withExtension:@"json"];
    NSData *data = [NSData dataWithContentsOfURL:fileURL];
    
    NSMutableArray *attachments = [[NSMutableArray alloc] init];
    
    for (NSUInteger i = 0; i < 4; i++)
    {
        BugsplatAttachment *attachment = [[BugsplatAttachment alloc] initWithFilename:[NSString stringWithFormat:@"generated%@.json", @(i)]
                                                                       attachmentData:data
                                                                          contentType:@"application/json"];
        
        [attachments addObject:attachment];
    }
    
    
    return attachments;
}
```

### Command line utility support

#### Step 1

Add "Other Linker Flags" build setting to embed Info.plist **`-sectcreate __TEXT __info_plist "$(SRCROOT)/BugsplatTesterCLI/Info.plist"`**

#### Step 2

Add `@executable_path/` to "Runtime Search Paths" build setting

#### Step 3

Follow the main configuration steps listed above, however, use upload-archive-cl.sh for uploading archives.

#### Step 4

Initialize as follows:

```text
[BugsplatStartupManager sharedManager].autoSubmitCrashReport = YES;
[BugsplatStartupManager sharedManager].askUserDetails = NO;
[[BugsplatStartupManager sharedManager] start];
```

#### Step 5

Given the "Runtime Search Paths" setting change in step 2, be sure any 3rd party dependencies are located in the same directory as the CLI program so they can be found at runtime.

## Sample Application

We have provided BugsplatTester as a sample application for you to test BugSplat. The quickest way to test BugSplat is to do the following:

#### Step 1

Clone the [BugsplatMac](https://github.com/BugSplatGit/BugsplatMac) repo and run the following:

```text
git submodule init
git submodule update
```

#### Step 2

Open the BugsplatTester.xcworkspace file. Edit the current scheme and uncheck "Debug executable" in the Run section, close the scheme editor and run the application.

#### Step 3

Click the "crash" button when prompted.

#### Step 4

Click the run button a second time in Xcode. The BugSplat crash dialog will appear the next time the app is launched.

#### Step 5

Fill out the crash dialog and submit the crash report.

#### Step 6

Visit BugSplat's [Crashes](https://app.bugsplat.com/v2/crashes) page. When prompted for credentials enter user "fred@bugsplat.com" and password "Flintstone". The crash you posted from BugsplatTester should be at the top of the list of crashes.

#### Step 7

Click the link in the "Crash Id" column to view more details about your crash.

#### Step 8

You will notice there are no function names or line numbers, this is because you need to upload the application's xcarchive. See step 2 in the "Configuration" section above for more information.

#### Step 9

Repeat this process with the executable from the xcarchive you created with BugsplatTester. To find the executable within the xcarchive, right click the xcarchive in finder and select "Show Package Contents". The executable should be located in .../Products/Applications/. BugSplat will display function names and line numbers for all crashes posted from this executable.

