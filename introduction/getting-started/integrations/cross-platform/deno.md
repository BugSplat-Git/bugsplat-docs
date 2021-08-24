# Deno

## Introduction

[BugSplat-Deno](https://deno.land/x/bugsplat@v1.0.0) is a TypeScript error reporting system for Deno applications. Before integrating a new BugSplat SDK with your application, make sure to review the [Getting Started](../../) resources and complete the simple startup tasks listed below.

* [Sign up](https://app.bugsplat.com/v2/sign-up) for a BugSplat account
* [Log in](https://app.bugsplat.com/auth0/login) using your email address
* Create a new [database](https://app.bugsplat.com/v2/company) for your application

{% hint style="info" %}
Need any further help? Check out the full BugSplat documentation [here](../../../../), or email the team at [support@bugsplat.com](mailto:support@bugsplat.com).
{% endhint %}

## Configuration

To add the bugsplat package to your application, run the following shell command at the root of your project’s directory:

```typescript
import { BugSplat } from 'https://deno.land/x/bugsplat@v1.0.0/bugsplat.ts';
```

Create a new instance of the BugSplat class with the name of your BugSplat database, the name of your application and the version of your application:

```typescript
const bugsplat = new BugSplat("DatabaseName", "AppName", "1.0.0.0");
```

Use bugsplat-deno to capture errors that originate inside of try-catch blocks:

```typescript
try {
  throw new Error("BugSplat");
} catch (error) {
  await bugsplat.post(error);
}
```

You can also use bugsplat-deno to post errors from non-fatal promise rejections:

```typescript
Promise.reject(new Error("BugSplat!")).catch(error => bugsplat.post(error, {}));
```

After posting an error with bugsplat-deno, navigate to the [Crashes](https://app.bugsplat.com/v2/crashes) page. You should see a new crash report for the application you just configured. Click the link in the ID column to see details about your crash on the [Crashes](https://app.bugsplat.com/v2/crashes) page:

![BugSplat deno crash example](https://www.bugsplat.com/assets/img/docs/deno-bugsplat-crash-example-1.png)

![BugSplat deno crash example ](https://www.bugsplat.com/assets/img/docs/deno-bugsplat-crash-example-2.png)

That’s it! Your application is now configured to post crash reports to BugSplat.

## API

In addition to the configuration demonstrated above, there are a few public methods that can be used to customize your BugSplat integration:

```typescript
bugsplat.setDefaultAppKey(appKey); // Additional metadata that can be queried via BugSplats web application
bugsplat.setDefaultUser(user); // The name or id of your user
bugsplat.setDefaultEmail(email); // The email of your user
bugsplat.setDefaultDescription(description); // Additional info about your crash that gets reset after every post
async bugsplat.post(error, options); // Posts an arbitrary Error object to BugSplat
// If the values options.appKey, options.user, options.email, options.description are set the corresponding default values will be overwritten
// Returns a promise that resolves with properties: error (if there was an error posting to BugSplat), response (the response from the BugSplat crash post API), and original (the error passed by bugsplat.post)
view rawbugsplat-deno-api.js hosted with ❤ by GitHub
Contributing
```

## Contributing

BugSplat loves open-source software! Please check out our project on [GitHub](https://github.com/BugSplat-Git/bugsplat-deno) and send us a Pull Request.

