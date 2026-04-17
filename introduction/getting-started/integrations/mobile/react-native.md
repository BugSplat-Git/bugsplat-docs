# React Native

### Introduction 👋

BugSplat's [`@bugsplat/expo`](https://www.npmjs.com/package/@bugsplat/expo) package provides crash and error reporting for React Native (Expo) apps across iOS, Android, and Web. BugSplat collects native crash reports, JavaScript errors, and custom metadata so that you can fix bugs and deliver a better user experience. Visit [bugsplat.com](https://www.bugsplat.com/) for more information and to sign up for an account.

| Platform | Native Crashes | JS Error Reporting |
|----------|---------------|-------------------|
| **iOS** | [bugsplat-apple](https://github.com/BugSplat-Git/bugsplat-apple) (PLCrashReporter) | HTTP POST to `/post/js/` |
| **Android** | [bugsplat-android](https://github.com/BugSplat-Git/bugsplat-android) (Crashpad) | HTTP POST to `/post/js/` |
| **Web** | N/A | [bugsplat-js](https://github.com/BugSplat-Git/bugsplat-js) |

### Requirements 📋

* The `bugsplat-android` SDK requires Android minSdk 26 (Android 8.0+).
* An [Expo](https://expo.dev/) managed or bare workflow project.

### Installation 🏗️

Install the `@bugsplat/expo` package:

```sh
npx expo install @bugsplat/expo
```

### Configuration ⚙️

Add the BugSplat config plugin to your `app.json` or `app.config.js`. Credentials for symbol upload can be set via environment variables (`BUGSPLAT_CLIENT_ID`, `BUGSPLAT_CLIENT_SECRET`) or directly in the plugin config.

```json
{
  "expo": {
    "plugins": [
      ["@bugsplat/expo", {
        "database": "your-database",
        "enableSymbolUpload": true
      }],
      ["expo-build-properties", {
        "android": {
          "minSdkVersion": 26
        }
      }]
    ]
  }
}
```

{% hint style="info" %}
If your project's `minSdkVersion` is already >= 26, the `expo-build-properties` plugin is not needed.
{% endhint %}

#### Plugin Options

| Option | Required | Description |
|--------|----------|-------------|
| `database` | No | BugSplat database name (can also be set via `init()` or `BUGSPLAT_DATABASE` env var) |
| `enableSymbolUpload` | No | Enable automatic symbol upload for iOS (dSYMs) and Android (.so files) |
| `symbolUploadClientId` | No | BugSplat API client ID (or set `BUGSPLAT_CLIENT_ID` env var) |
| `symbolUploadClientSecret` | No | BugSplat API client secret (or set `BUGSPLAT_CLIENT_SECRET` env var) |

### Usage 🧑‍💻

#### Initialization

Call `init()` before using any other BugSplat functions. Replace `your-database`, `YourApp`, and `1.0.0` with your BugSplat database name, application name, and version.

```typescript
import { init } from '@bugsplat/expo';

await init('your-database', 'YourApp', '1.0.0', {
  userName: 'user@example.com',
  userEmail: 'user@example.com',
  appKey: 'optional-key',
});
```

**Options:**

* `appKey?: string` — Queryable metadata key
* `userName?: string` — User name for reports
* `userEmail?: string` — User email for reports
* `autoSubmitCrashReport?: boolean` — Auto-submit crashes (iOS only, default: `true`)
* `attributes?: Record<string, string>` — Custom key-value attributes
* `attachments?: string[]` — File paths to attach (native only)
* `description?: string` — Default description

#### Reporting Errors

Manually report caught errors using `post()`:

```typescript
import { post } from '@bugsplat/expo';

try {
  riskyOperation();
} catch (error) {
  const result = await post(error);
  console.log(result.success ? 'Reported!' : result.error);
}
```

#### Error Boundary

Wrap your component tree in `<ErrorBoundary>` to catch React render errors and report them to BugSplat automatically. This works on all platforms — iOS, Android, and Web.

```tsx
import { ErrorBoundary } from '@bugsplat/expo';

function App() {
  return (
    <ErrorBoundary fallback={<Text>Something went wrong</Text>}>
      <MyComponent />
    </ErrorBoundary>
  );
}
```

The `fallback` prop accepts a React node or a render function:

```tsx
<ErrorBoundary
  fallback={({ error, resetErrorBoundary }) => (
    <View>
      <Text>{error.message}</Text>
      <Button title="Try again" onPress={resetErrorBoundary} />
    </View>
  )}
>
  <MyComponent />
</ErrorBoundary>
```

#### User Feedback

In addition to crash reporting, BugSplat supports collecting non-crashing user feedback such as bug reports and feature requests. Feedback reports appear in BugSplat with the "User Feedback" type, grouped by title. This works on all platforms — iOS, Android, and Web.

Submit feedback imperatively from anywhere after `init()`:

```typescript
import { postFeedback } from '@bugsplat/expo';

const result = await postFeedback('Login button broken', {
  description: 'Nothing happens when I tap sign in',
});
console.log(result.success ? `Feedback #${result.crashId} posted` : result.error);
```

**Options:**

* `description?: string` — Longer body of the feedback
* `user?: string` — Override default user name
* `email?: string` — Override default user email
* `appKey?: string` — Override default app key

Or drive a feedback form with the `useFeedback` hook, which tracks loading and error state:

```tsx
import { useFeedback } from '@bugsplat/expo';

function FeedbackForm() {
  const { postFeedback, loading, error } = useFeedback();

  return (
    <Button
      title={loading ? 'Sending…' : 'Send feedback'}
      disabled={loading}
      onPress={() =>
        postFeedback('Login button broken', {
          description: 'Nothing happens when I tap sign in',
        })
      }
    />
  );
}
```

#### User Info

Update user info for subsequent reports:

```typescript
import { setUser } from '@bugsplat/expo';

setUser('Jane Doe', 'jane@example.com');
```

#### Attributes

Set custom key-value attributes that are searchable in the BugSplat dashboard:

```typescript
import { setAttribute } from '@bugsplat/expo';

setAttribute('environment', 'production');
```

{% hint style="info" %}
Custom attributes via `setAttribute` are not supported on web.
{% endhint %}

### Symbol Upload 📦

Production crash reports require debug symbols to produce readable stack traces. When `enableSymbolUpload` is set in your plugin config, symbols are uploaded automatically during iOS and Android release builds.

For manual uploads or CI/CD workflows, use [`@bugsplat/symbol-upload`](https://github.com/BugSplat-Git/symbol-upload):

```sh
npm install --save-dev @bugsplat/symbol-upload
```

**iOS dSYMs:**

```sh
npx @bugsplat/symbol-upload \
  -b your-database -a YourApp -v 1.0.0 \
  -i $BUGSPLAT_CLIENT_ID -s $BUGSPLAT_CLIENT_SECRET \
  -d /path/to/build/Products/Release-iphoneos \
  -f "**/*.dSYM"
```

**Android .so files:**

```sh
npx @bugsplat/symbol-upload \
  -b your-database -a YourApp -v 1.0.0 \
  -i $BUGSPLAT_CLIENT_ID -s $BUGSPLAT_CLIENT_SECRET \
  -d android/app/build/intermediates/merged_native_libs \
  -f "**/*.so" -m
```

**JavaScript source maps** (after `npx expo export --source-maps`):

```sh
npx @bugsplat/symbol-upload \
  -b your-database -a YourApp -v 1.0.0 \
  -i $BUGSPLAT_CLIENT_ID -s $BUGSPLAT_CLIENT_SECRET \
  -d dist \
  -f "**/*.map"
```

Run `npx @bugsplat/symbol-upload --help` for all options.

### Testing Native Crashes 🧪

To test native crash reporting, you must run a **release build** — the debugger intercepts crashes in debug builds.

```sh
# iOS
npx expo run:ios --configuration Release

# Android
npx expo run:android --variant release
```

Trigger a test crash to verify your integration:

```typescript
import { crash } from '@bugsplat/expo';

// Triggers a native crash (iOS/Android) or throws an error (web)
crash();
```

{% hint style="info" %}
**iOS:** Crash reports are captured at crash time and **uploaded on the next app launch** when `init()` is called again. After triggering a test crash, relaunch the app to upload the pending report.

**Android:** Crash reports are captured and **uploaded immediately at crash time** by the Crashpad handler process.
{% endhint %}

### Troubleshooting 🔧

#### Android: Crashes not uploading on emulator

The Crashpad handler process requires native libraries to be extracted to disk. The `@bugsplat/expo` config plugin sets `extractNativeLibs=true` automatically. If you're still not seeing crashes:

* Use a **`google_apis`** emulator image (not `google_apis_playstore`). The Play Store emulator images have restrictions that prevent Crashpad's handler process from executing.
* Alternatively, test on a **physical Android device** where this is not an issue.

#### iOS: No crash report after test crash

Make sure you **relaunch the app and call `init()` again** after the crash. PLCrashReporter saves the crash to disk and uploads it on the next launch.

{% hint style="info" %}
**Not finding what you're looking for?** Please reach out to [support@bugsplat.com](mailto:support@bugsplat.com) for further assistance.
{% endhint %}
