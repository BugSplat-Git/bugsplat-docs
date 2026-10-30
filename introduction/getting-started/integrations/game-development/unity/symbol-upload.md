# Unity Symbol Upload

BugSplat for Unity uploads [symbol files](../../../../development/working-with-symbol-files/README.md) as a post-build step, so crash reports symbolicate to function names, file names, and line numbers. Which files are uploaded, and the option that turns the upload on, is covered on each platform's page: [Windows](windows.md#windows-symbols), [macOS](macos.md#macos-symbols), [iOS](ios.md#ios-symbols), and [Android](android.md#android-symbols).

### Credentials

The upload authenticates with an OAuth2 [Client ID and Client Secret](../../../../development/web-services/oauth2.md#client-credentials), generated on BugSplat's [Integrations](https://app.bugsplat.com/v2/settings/database/integrations) page. Credentials are **specific to one database** and are never stored in your project — an asset carrying them would end up in version control and inside shipped builds. They resolve in this order:

1. **`SYMBOL_UPLOAD_CLIENT_ID` / `SYMBOL_UPLOAD_CLIENT_SECRET` environment variables** — use these in CI. They are the names the [symbol-upload](../../../../development/working-with-symbol-files/upload-symbols-with-symbol-upload.md) CLI reads, so the same pair works whether Unity runs the upload or your CI runs `xcodebuild` itself.
2. **`~/.bugsplat/credentials/<database>.sh`** — for local development. Set it from **BugSplat > Symbol Upload > Set Credentials**, which writes one file per database, so a machine can hold credentials for as many databases as you work with.

**Clear Credentials** deletes the current project's file, and **Check Credentials** reports which source a build would use. When neither source supplies both values, symbol upload is skipped with a warning and the build still succeeds — on iOS as an Xcode build warning, since that upload runs during the Xcode build rather than the Unity one.

Because the file lives in your home directory rather than the project, there is nothing to add to `.gitignore` and nothing to strip out of a build.

### Environment Variables

| Variable                      | Description                                                                                                          |
| ----------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| `SYMBOL_UPLOAD_CLIENT_ID`     | OAuth2 Client ID from the [Integrations](https://app.bugsplat.com/v2/settings/database/integrations) page. Takes precedence over `~/.bugsplat/credentials/<database>.sh`. |
| `SYMBOL_UPLOAD_CLIENT_SECRET` | OAuth2 Client Secret from the [Integrations](https://app.bugsplat.com/v2/settings/database/integrations) page. Takes precedence over `~/.bugsplat/credentials/<database>.sh`. |

{% hint style="warning" %}
**Upgrading from 4.x:** `SymbolUploadClientId` and `SymbolUploadClientSecret` have been removed from `BugSplatOptions`, and the environment variables are renamed from `BUGSPLAT_CLIENT_ID`/`BUGSPLAT_CLIENT_SECRET`. Move your credentials to the menu or the new variables. **If an options asset holding credentials has ever been committed, rotate them** — prior versions serialized both values into player builds and into the generated `project.pbxproj`.
{% endhint %}
