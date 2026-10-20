# Source Maps

BugSplat can convert minified JavaScript stack traces back into the function names, file names, and line numbers of your original source. To do that, upload your [source map](https://developer.mozilla.org/en-US/docs/Tools/Debugger/How\_to/Use\_a\_source\_map) files to BugSplat for every version you release, using [@bugsplat/symbol-upload](upload-symbols-with-symbol-upload.md).

## How BugSplat Matches Source Maps

BugSplat finds the right source map for each stack frame using three things:

* **Database, application, and version.** Source maps are stored per database, application, and version. The application name and version you pass to the BugSplat client (for example `new BugSplat(database, application, version)`) must exactly match the values you upload source maps with. If they differ, the report won't be mapped.
* **File name.** Each source map's file name must be the name of the JavaScript file it describes, plus `.map`. For instance, if your build generates `main.153ee63b.chunk.js`, the source map must be named `main.153ee63b.chunk.js.map`. Every bundler listed below names its source maps this way by default.
* **One upload per release.** Upload the source maps from the same build you deploy, every time you release a new version.

{% hint style="info" %}
If BugSplat can't map a stack trace back to the original source, the **Debugger Output** tab on the Crash page shows diagnostic information from the BugSplat backend to help you troubleshoot.
{% endhint %}

## Credentials

symbol-upload authenticates with an OAuth2 client ID and secret. Create a pair on the [Integrations](https://app.bugsplat.com/v2/settings/database/integrations) page in BugSplat, then provide them through the `SYMBOL_UPLOAD_CLIENT_ID` and `SYMBOL_UPLOAD_CLIENT_SECRET` environment variables (or the `-i` and `-s` arguments). Your database can be provided with `-b` or the `BUGSPLAT_DATABASE` environment variable. Keep these values in your CI secrets rather than in source control.

## Uploading

Install symbol-upload as a development dependency (it requires Node.js 22.12 or later):

```sh
npm i -D @bugsplat/symbol-upload
```

Then run it against your build output after each production build:

```sh
npx symbol-upload -b your-database -a your-application -v 1.2.0 -d dist -f "**/*.js.map"
```

If you leave out `-a` and `-v`, symbol-upload reads `name` and `version` from the `package.json` in the current directory.

### Keeping the Version in Sync

The simplest way to keep the uploaded version and the version your app reports identical is to take both from `package.json`. Run the upload as an npm script so `-a` and `-v` default to your `package.json` values:

```json
{
  "scripts": {
    "build": "vite build",
    "postbuild": "symbol-upload -d dist -f \"**/*.js.map\""
  }
}
```

And inject the same values into your bundle at build time, for example with Vite's `define` (npm sets `npm_package_name` and `npm_package_version` while running a script):

```typescript
// vite.config.ts
import { defineConfig } from 'vite';

export default defineConfig({
  define: {
    __APP_NAME__: JSON.stringify(process.env.npm_package_name),
    __APP_VERSION__: JSON.stringify(process.env.npm_package_version),
  },
  build: {
    sourcemap: true,
  },
});
```

```typescript
// src/main.ts
declare const __APP_NAME__: string;
declare const __APP_VERSION__: string;

const bugsplat = new BugSplat('your-database', __APP_NAME__, __APP_VERSION__);
```

webpack's [DefinePlugin](https://webpack.js.org/plugins/define-plugin/) works the same way.

## Bundler Recipes

### Vite

Enable source maps in `vite.config.ts`:

```typescript
export default defineConfig({
  build: {
    sourcemap: true,
  },
});
```

Vite writes the bundles and their maps to `dist/assets`. Upload them after `vite build`:

```sh
npx symbol-upload -d dist -f "**/*.js.map"
```

### webpack

Set `devtool` for production builds in `webpack.config.js`:

```javascript
module.exports = {
  mode: 'production',
  devtool: 'source-map',
  // ...
};
```

webpack writes each `.js.map` next to its bundle in your `output.path` (`dist` by default). Upload them after the build:

```sh
npx symbol-upload -d dist -f "**/*.js.map"
```

### Next.js

Next.js doesn't generate browser source maps for production builds unless you enable them in `next.config.js`:

```javascript
module.exports = {
  productionBrowserSourceMaps: true,
};
```

After `next build`, the browser bundles and their maps are in `.next/static`. Upload them:

```sh
npx symbol-upload -d .next/static -f "**/*.js.map"
```

### Expo and React Native

The `@bugsplat/expo` config plugin uploads JavaScript source maps, iOS dSYMs, and Android native symbols for you during release builds when `enableSymbolUpload` is set. For manual uploads or CI, the [React Native](../../getting-started/integrations/mobile/react-native.md) guide's **Symbol Upload** section shows the commands, including uploading source maps after `npx expo export --source-maps`.

{% content-ref url="../../getting-started/integrations/mobile/react-native.md" %}
[react-native.md](../../getting-started/integrations/mobile/react-native.md)
{% endcontent-ref %}

## Serving Source Maps

BugSplat doesn't need your source maps to be deployed. If you don't want them publicly available, upload them to BugSplat and then remove the `.map` files from the output you deploy.

## Continuous Integration

symbol-upload is also available as a GitHub Action. Set `files` to `"**/*.js.map"` and `directory` to your build output; see [Upload Symbols with symbol-upload](upload-symbols-with-symbol-upload.md) for the full action and command-line reference.
