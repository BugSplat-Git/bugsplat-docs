# Vue

## Integration

To begin integrating your app with BugSplat, install the bugsplat package via npm:

```
npm i bugsplat
```

Create a BugSplat instance at the entry point of your application (usually `main.js` or `main.ts`), replacing the database, application, and version with the values for your application.

```typescript
import { createApp } from 'vue';
import { BugSplat } from 'bugsplat';
import App from './App.vue';

const bugsplat = new BugSplat('your-bugsplat-database', 'my-vue-app', '1.0.0');
```

Post errors from Vue components with [`app.config.errorHandler`](https://vuejs.org/api/application.html#app-config-errorhandler). Vue calls it for errors thrown during rendering, in lifecycle hooks, watchers, event handlers, and `setup()`, and passes an `info` string that says where the error came from. Errors Vue handles this way don't reach `window.onerror`.

```typescript
const app = createApp(App);

app.config.errorHandler = (error, instance, info) => {
  console.error(error);
  bugsplat.post(error as Error, { attributes: { vueInfo: info } });
};

app.mount('#app');
```

{% hint style="info" %}
Setting `errorHandler` replaces Vue's default logging, so call `console.error` yourself if you still want errors in the browser console.
{% endhint %}

Also listen for errors and unhandled promise rejections that happen outside of Vue, such as in timers or code that runs before the app mounts.

```typescript
window.addEventListener('error', (event) => {
  bugsplat.post(event.error);
});

window.addEventListener('unhandledrejection', (event) => {
  bugsplat.post(event.reason);
});
```

You can also use BugSplat to capture errors in catch blocks.

```typescript
try {
    throw new Error('BugSplat rocks!');
} catch (error) {
    await bugsplat.post(error);
}
```

{% hint style="info" %}
The `application` and `version` values must match the ones you upload source maps with. See [Source Maps](../../../development/working-with-symbol-files/source-maps.md) to map minified stack traces back to your original code.
{% endhint %}

If the error has a `cause`, bugsplat captures the chain automatically (up to 100 linked errors) and shows each as a `Caused by:` row; see [Error Chains](javascript.md#error-chains) for details.

Wrap a low-level error and rethrow it; the handlers above post the wrapper and bugsplat captures the cause with it.

```typescript
function loadUserSettings() {
  try {
    JSON.parse('{ not valid json');
  } catch (error) {
    throw new Error('Failed to load user settings', { cause: error });
  }
}

loadUserSettings();
```

BugSplat shows `Error: Failed to load user settings` first, then a `Caused by: SyntaxError` row with its own stack frames.

After posting an error, navigate to the [Crashes](https://app.bugsplat.com/v2/crashes) page in BugSplat and you should see a new error report for the application you just configured. Click the link in the ID column to see details about your crash on the Crash page.
