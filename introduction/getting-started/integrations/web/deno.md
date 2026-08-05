# Deno

## 👀 Getting Started

Adding BugSplat error reporting to your [Deno](https://deno.com/) application, web worker, or serverless function only takes a few lines of code. Once integrated, BugSplat will capture stack traces, logs, and other valuable insight when your code encounters a problem. BugSplat's [bugsplat-js](https://github.com/BugSplat-Git/bugsplat-js) package is fully typed and easily imported via [esm.sh](https://esm.sh/).

## ⚙️ Integrating

Add an import from [bugsplat-js](https://github.com/BugSplat-Git/bugsplat-js) via [esm.sh](https://esm.sh/):

```typescript
import { BugSplat } from 'https://esm.sh/bugsplat@8.0.1';
```

Create a new instance of the `BugSplat` class with the name of your BugSplat database, the name of your application, and the version of your application:

```typescript
const bugsplat = new BugSplat('DatabaseName', 'AppName', '1.0.0');
```

Use the `bugsplat` instance to capture errors that originate inside of try-catch blocks:

```typescript
try {
  throw new Error("BugSplat");
} catch (error) {
  await bugsplat.post(error);
}
```

Use `bugsplat` to post errors from promise rejections:

```typescript
Promise.reject(new Error("BugSplat!")).catch(error => bugsplat.post(error, {}));
```

You can also attach a file to your error post by passing a `BugSplatOptions` object containing with a value for `additionalFormDataParams` property.

```typescript
await bugsplat.post(error, getAdditionalOptionsWithLogFile());

function getAdditionalOptionsWithLogFile() {
  const filename = 'log.txt';
  return {
    additionalFormDataParams: [{
      key: 'file',
      value: new File([Deno.readFileSync(filename)], filename),
      filename: filename,
    }],
  };
}
```

## 🧪 Sample

Clone the [my-deno-crasher](https://github.com/BugSplat-Git/my-deno-crasher) repository:

```bash
git clone https://github.com/BugSplat-Git/my-deno-crasher
```

Set your current directory to `my-deno-crasher`.

```bash
cd my-deno-crasher
```

Run the sample with the `--allow-net` and `--allow-read` flags to allow BugSplat to post an error to our backend and read the log file respectively.

```bash
deno run --allow-net --allow-read main.ts
```

## 📈 Viewing Reports

After posting an error, navigate to the [Crashes](https://app.bugsplat.com/v2/crashes) page. You should see a new crash report for the application you just configured. Click the link in the ID column to see details about your crash on the [Crashes](https://app.bugsplat.com/v2/crashes) page:

<figure><img src="../../../../.gitbook/assets/image (33).png" alt=""><figcaption><p>Deno Error Reports</p></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (32).png" alt=""><figcaption><p>Deno Error Report</p></figcaption></figure>

That’s it! Your application is now configured to post error reports to BugSplat.

## 🧩 API

In addition to the configuration demonstrated above, there are a few public methods that can be used to customize your BugSplat integration:

```typescript
bugsplat.setDefaultAppKey(appKey); // Additional metadata that can be queried via BugSplats web application
bugsplat.setDefaultUser(user); // The name or id of your user
bugsplat.setDefaultEmail(email); // The email of your user
bugsplat.setDefaultDescription(description); // Additional info about your crash that gets reset after every post
async bugsplat.post(error, options); // Posts an arbitrary Error object to BugSplat
// If the values options.appKey, options.user, options.email, options.description are set the corresponding default values will be overwritten
// Returns a promise that resolves with properties: error (if there was an error posting to BugSplat), response (the response from the BugSplat crash post API), and original (the error passed by bugsplat.post)
```

## 🤝 Contributing

BugSplat loves open-source software! Please check out our project on [GitHub](https://github.com/BugSplat-Git/bugsplat-js) and send us a Pull Request.
