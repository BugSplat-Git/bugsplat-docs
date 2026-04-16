# Android

### Introduction 👋

The `bugsplat-android` library enables posting native crash reports, Application Not Responding (ANR) events, and user feedback to BugSplat from Android devices. Visit [bugsplat.com](https://www.bugsplat.com/) for more information and to sign up for an account.

### Requirements 📋

* **Android Gradle Plugin (AGP)**: 8.5.1 or higher (for 16KB page size support)
* **Android NDK**: r27 or higher recommended
* **minSdkVersion**: 21 or higher
* **targetSdkVersion**: 35 or higher recommended

{% hint style="info" %}
Starting November 1st, 2025, Google Play requires all new apps and updates targeting Android 15+ to [support 16 KB page sizes](https://developer.android.com/guide/practices/page-sizes). The BugSplat Android SDK is built with 16KB ELF alignment to comply with this requirement.
{% endhint %}

### Integration 🏗️

`bugsplat-android` supports multiple installation methods.

#### Gradle (Recommended)

Add the BugSplat dependency to your app's `build.gradle` file:

```gradle
dependencies {
    implementation 'com.bugsplat:bugsplat-android:1.2.1'
}
```

BugSplat is hosted on Maven Central, which is included by default in most Android projects. If needed, ensure `mavenCentral()` is in your `settings.gradle.kts`:

```kotlin
dependencyResolutionManagement {
    repositories {
        google()
        mavenCentral()
    }
}
```

#### Manual Setup

To integrate BugSplat using the AAR file:

1. Go to the [Releases](https://github.com/BugSplat-Git/bugsplat-android/releases) page and download the latest `bugsplat-android-x.y.z.aar` file.
2. Copy the AAR into your app module's `libs/` directory.
3. In your app-level `build.gradle`, add:

```gradle
dependencies {
    implementation files('libs/bugsplat-android-x.y.z.aar')
}
```

4. In your `AndroidManifest.xml`, add the required permissions:

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

### Usage 🧑‍💻

#### Loading config from local.properties (recommended)

Keeping your `database`, `application`, and `version` values in one place avoids drift between runtime (`BugSplat.init`) and symbol upload. The pattern most BugSplat users adopt:

**1.** Add the database name to the gitignored `local.properties`:

```properties
bugsplat.database=your_database
```

**2.** In your app module's `build.gradle`, load it and expose it (plus `applicationId` and `versionName`) as `BuildConfig` fields:

```gradle
def localProps = new Properties()
def localPropsFile = rootProject.file('local.properties')
if (localPropsFile.exists()) {
    localPropsFile.withInputStream { localProps.load(it) }
}

android {
    defaultConfig {
        applicationId "com.example.myapp"
        versionName "1.0.0"

        buildConfigField "String", "BUGSPLAT_DATABASE",
            "\"${localProps.getProperty('bugsplat.database')}\""
        buildConfigField "String", "BUGSPLAT_APP_NAME",
            "\"${applicationId}\""
        buildConfigField "String", "BUGSPLAT_APP_VERSION",
            "\"${versionName}\""
    }
    buildFeatures { buildConfig true }
}
```

{% hint style="info" %}
This same `bugsplat.database` value is picked up by the symbol upload task below, so there's a single source of truth across the whole build. See [`example/build.gradle`](https://github.com/BugSplat-Git/bugsplat-android/blob/main/example/build.gradle) for the complete setup.
{% endhint %}

#### Initialization

Initialize BugSplat from your launch `Activity` — typically in `onCreate`:

**Java**

```java
import com.bugsplat.android.BugSplat;

public class MainActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        BugSplat.init(
            this,
            BuildConfig.BUGSPLAT_DATABASE,
            BuildConfig.BUGSPLAT_APP_NAME,
            BuildConfig.BUGSPLAT_APP_VERSION
        );
    }
}
```

**Kotlin**

```kotlin
import com.bugsplat.android.BugSplat

class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        BugSplat.init(
            this,
            BuildConfig.BUGSPLAT_DATABASE,
            BuildConfig.BUGSPLAT_APP_NAME,
            BuildConfig.BUGSPLAT_APP_VERSION
        )
    }
}
```

#### Custom Attributes

Attach arbitrary key/value pairs to crash reports. Attributes can be set at init time or updated at any time afterward:

```java
// At init
Map<String, String> attributes = new HashMap<>();
attributes.put("environment", "development");
attributes.put("build_flavor", "internal");
BugSplat.init(
    this,
    BuildConfig.BUGSPLAT_DATABASE,
    BuildConfig.BUGSPLAT_APP_NAME,
    BuildConfig.BUGSPLAT_APP_VERSION,
    attributes
);

// Or at any time after init
BugSplat.setAttribute("user_tier", "premium");
BugSplat.removeAttribute("user_tier");
```

Keys and values are each limited to 255 bytes.

#### Attachments

Attach files to crash reports by passing their paths to `init`:

```java
String attachmentPath = getFileStreamPath("log.txt").getAbsolutePath();
String[] attachments = new String[]{attachmentPath};
BugSplat.init(
    this,
    BuildConfig.BUGSPLAT_DATABASE,
    BuildConfig.BUGSPLAT_APP_NAME,
    BuildConfig.BUGSPLAT_APP_VERSION,
    attachments
);
```

#### ANR Detection

`bugsplat-android` automatically detects and reports Application Not Responding (ANR) events on Android 11+ (API 30+) using the [`ApplicationExitInfo`](https://developer.android.com/reference/android/app/ApplicationExitInfo) API.

When the system kills your app due to an ANR, the event is recorded by Android. On the next app launch, the SDK queries `ActivityManager.getHistoricalProcessExitReasons()` for new ANRs, reads the system-provided thread dump, and uploads it to BugSplat. ANR reports appear alongside crashes with the **"Android.ANR"** type.

The thread dump includes:

* Full Java stack traces for all threads in the process
* Native stack frames with BuildIds (symbolicated against uploaded `.sym` files)
* Lock contention information

ANR detection is enabled automatically when you call `BugSplat.init()` — no additional configuration required. The SDK persists the timestamp of the last reported ANR in `SharedPreferences` to avoid duplicate uploads.

{% hint style="info" %}
ANR detection requires Android 11+ (API 30+). On older versions, `ApplicationExitInfo` is unavailable and ANR detection is silently disabled.
{% endhint %}

To test ANR detection, call `BugSplat.hang()` from the main thread:

```java
// Blocks the main thread in a native infinite loop.
// Tap the screen to trigger the system ANR dialog (~5s).
BugSplat.hang();
```

#### User Feedback

Submit non-crashing feedback (bug reports, feature requests) using `BugSplat.postFeedback`. Feedback reports appear in BugSplat with the **"User Feedback"** type.

```java
// Async (returns immediately, runs on background thread)
BugSplat.postFeedback(
    BuildConfig.BUGSPLAT_DATABASE,
    BuildConfig.BUGSPLAT_APP_NAME,
    BuildConfig.BUGSPLAT_APP_VERSION,
    "Login button broken",      // title (required)
    "Nothing happens on tap",   // description
    "Jane",                     // user
    "jane@example.com",         // email
    null                        // appKey
);

// Blocking variant (returns true on success)
boolean ok = BugSplat.postFeedbackBlocking(
    BuildConfig.BUGSPLAT_DATABASE,
    BuildConfig.BUGSPLAT_APP_NAME,
    BuildConfig.BUGSPLAT_APP_VERSION,
    "Login button broken", "Nothing happens on tap",
    "Jane", "jane@example.com", null
);
```

File attachments can be included by passing a `List<File>`:

```java
List<File> attachments = new ArrayList<>();
attachments.add(new File(getFilesDir(), "app.log"));

BugSplat.postFeedback(
    BuildConfig.BUGSPLAT_DATABASE,
    BuildConfig.BUGSPLAT_APP_NAME,
    BuildConfig.BUGSPLAT_APP_VERSION,
    "Login button broken", "Nothing happens on tap",
    "Jane", "jane@example.com", null,
    attachments
);
```

Custom key/value attributes can also be attached to the feedback report. They're JSON-encoded into the `attributes` field on the commit request:

```java
Map<String, String> attributes = new HashMap<>();
attributes.put("environment", "production");
attributes.put("user_tier", "premium");

BugSplat.postFeedback(
    BuildConfig.BUGSPLAT_DATABASE,
    BuildConfig.BUGSPLAT_APP_NAME,
    BuildConfig.BUGSPLAT_APP_VERSION,
    "Login button broken", "Nothing happens on tap",
    "Jane", "jane@example.com", null,
    null,        // attachments
    attributes
);
```

#### Native Library Packaging

To ensure native libraries (and their debug info) are properly deployed, configure your app's `build.gradle`:

```gradle
android {
    defaultConfig {
        ndk {
            // Match the ABI filters from the library
            abiFilters 'arm64-v8a', 'x86_64', 'armeabi-v7a'
        }
    }

    packagingOptions {
        jniLibs {
            keepDebugSymbols += ['**/*.so']
            // Use uncompressed shared libraries with 16KB zip alignment
            // (required for Android 15+ devices with 16KB page sizes).
            useLegacyPackaging false
        }
        doNotStrip '**/*.so'
    }
}
```

### Symbol Upload 🔣

To symbolicate native stack frames in crash and ANR reports, upload your app's unstripped `.so` files to BugSplat as Breakpad `.sym` files. There are three ways to do this.

#### 1. Gradle Build Integration (Recommended)

Wire symbol upload into your Gradle build so it runs automatically after `assembleDebug` / `assembleRelease`. Credentials are loaded from the gitignored `local.properties` to avoid committing them.

**Step 1 — Add credentials to `local.properties`:**

```properties
bugsplat.database=your_database
bugsplat.clientId=your_client_id
bugsplat.clientSecret=your_client_secret
```

**Step 2 — In `build.gradle`, load credentials and register per-ABI upload tasks:**

```gradle
def localProps = new Properties()
def localPropsFile = rootProject.file('local.properties')
if (localPropsFile.exists()) {
    localPropsFile.withInputStream { localProps.load(it) }
}

ext {
    bugsplatDatabase = localProps.getProperty('bugsplat.database')
    bugsplatClientId = localProps.getProperty('bugsplat.clientId', '')
    bugsplatClientSecret = localProps.getProperty('bugsplat.clientSecret', '')
    bugsplatAppName = android.defaultConfig.applicationId
    bugsplatAppVersion = android.defaultConfig.versionName
}

// See the example app for the full implementation, which:
//  - Downloads the symbol-upload binary if not present
//  - Registers per-ABI upload tasks (one per ABI in defaultConfig.ndk.abiFilters)
//  - Chains the per-ABI tasks serially in an AllAbis task via mustRunAfter
//    (the symbol-upload binary uses a shared temp dir)
//  - Reads merged_native_libs from AGP 8.6+ intermediate path:
//    build/intermediates/merged_native_libs/{buildType}/merge{BuildType}NativeLibs/out/lib/{abi}/

tasks.whenTaskAdded { task ->
    if (task.name == 'assembleDebug') {
        task.finalizedBy(tasks.named('uploadBugSplatSymbolsDebugAllAbis'))
    }
}
```

See [`example/build.gradle`](https://github.com/BugSplat-Git/bugsplat-android/blob/main/example/build.gradle) in the SDK repo for the complete, copy-pasteable implementation.

Get your `clientId` and `clientSecret` from the [BugSplat Integrations page](https://app.bugsplat.com/v2/database/integrations#oauth).

#### 2. Built-in Programmatic Upload

The SDK exposes `BugSplat.uploadSymbols` to upload symbols at runtime (useful if you need to kick this off from app code rather than Gradle):

```java
// Async
BugSplat.uploadSymbols(
    context,
    "your_database",
    "your_app",
    "1.0.0",
    "your_client_id",
    "your_client_secret",
    nativeLibsDir
);

// Blocking
try {
    BugSplat.uploadSymbolsBlocking(
        context, "your_database", "your_app", "1.0.0",
        "your_client_id", "your_client_secret", nativeLibsDir
    );
} catch (IOException e) {
    Log.e("MyApp", "Failed to upload symbols", e);
}
```

This requires bundling the `symbol-upload` binary in your app's assets. See the [Example App README](https://github.com/BugSplat-Git/bugsplat-android/blob/main/example/README.md) for details.

#### 3. Command-Line Tool

You can also invoke [`symbol-upload`](../../../../education/faq/how-to-upload-symbol-files-with-symbol-upload.md) directly from the command line:

```sh
# Download the binary
curl -sL -O "https://app.bugsplat.com/download/symbol-upload-macos" && chmod +x symbol-upload-macos

# Upload .sym files generated from unstripped .so files
./symbol-upload-macos \
    -b DATABASE -a APPLICATION -v VERSION \
    -i CLIENT_ID -s CLIENT_SECRET \
    -d /path/to/native/libs \
    -f "**/*.so" -m
```

Please refer to the [symbol-upload documentation](../../../../education/faq/how-to-upload-symbol-files-with-symbol-upload.md) for full usage details.

### Sample Application 🧑‍🏫

The `bugsplat-android` repository includes a full example app that demonstrates:

* Initializing the SDK at app startup
* Triggering a native crash
* Triggering an ANR (via `BugSplat.hang()`) to test ANR detection and native frame symbolication
* Submitting user feedback via a dialog
* Setting custom attributes via a dialog
* Uploading symbols via Gradle

To run it:

1. Clone [`bugsplat-android`](https://github.com/BugSplat-Git/bugsplat-android).
2. Open in Android Studio.
3. Add your BugSplat credentials to `local.properties` as described in the **Symbol Upload** section above.
4. Select the `example` run configuration and click Run.

See the [Example App README](https://github.com/BugSplat-Git/bugsplat-android/blob/main/example/README.md) for more details.
