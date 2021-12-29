# Vue

## Introduction

BugSplat's [npm package](https://www.npmjs.com/package/bugsplat) provides error reporting for Vue applications. Before integrating your application with BugSplat, make sure to review the [Getting Started](../../) resources and complete the simple startup tasks listed below.

* [Sign up](https://app.bugsplat.com/v2/sign-up) for a BugSplat account
* [Log in](https://app.bugsplat.com/auth0/login) using your email address
* Create a new [database](https://app.bugsplat.com/v2/settings/company/databases) for your application

{% hint style="info" %}
Need any further help? Check out the full BugSplat documentation [here](../../../../), or email the team at [support@bugsplat.com](mailto:support@bugsplat.com).
{% endhint %}

## Integration

To begin integrating your app with BugSplat, install the bugsplat package via npm:

```
npm i bugsplat
```

Import or require BugSplat at the entry point of your application.

```typescript
import { BugSplat } from 'bugsplat';
```

Add a database property to your package.json.

```typescript
{
  "name": "my-vue-crasher",
  "version": "1.0.0",
  "database": "{{ your database here}}",
  ...
}
```

In index.js, create a new instance of BugSplat passing the constructor values for database, application, and version from your package.json.

```typescript
const packageJson = require('../package.json');
const bugsplat = new BugSplat(packageJson.database, packageJson.name, packageJson.version);
```

Add event handlers to window.onunhandledrejection and window.onerror so that errors are posted to BugSplat.

```typescript
window.onunhandledrejection = async (rejection) => {
  await bugsplat.post(rejection.reason);
}

window.onerror = async (event, source, lineno, colno, error) => {
  await bugsplat.post(error);
}
```

You can also use BugSplat to capture errors in catch blocks.

```typescript
try {
    throw new Error('BugSplat rocks!');
} catch (error) {
    await bugsplat.post(error);
}
```

After posting an error, navigate to the [Crashes](https://app.bugsplat.com/v2/crashes) page in BugSplat and you should see a new error report for the application you just configured. Click the link in the ID column to see details about your crash on the Crash page.
