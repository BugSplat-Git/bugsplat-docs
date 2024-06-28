# Angular

## Overview

BugSplat supports the collection of errors in Angular applications. The bugsplat-ng npm package implements Angular’s [ErrorHandler](https://angular.io/api/core/ErrorHandler) interface in order to post errors to BugSplat where they can be tracked and managed.

## Sample

This repository includes a sample my-angular-crasher application that has be pre-configured with BugSplat. A hosted version of my-angular-crasher can also be found [here](https://bugsplat-git.github.io/my-angular-crasher). Before you try the sample, you'll need to create an OAuth2 ClientId/ClientSecret pair as shown [here](https://docs.bugsplat.com/introduction/development/web-services/oauth2).

Once you've genereated OAuth2 credentials, create a file with the name `.env` at the root of the repository and populate with the correct values substituted for `{{clientId}}` and `{{clientSecret}}`:

```
SYMBOL_UPLOAD_CLIENT_ID={{clientId}}
SYMBOL_UPLOAD_CLIENT_SECRET={{clientSecret}}
```

To test the sample, perform the following steps:

1. `git clone https://github.com/BugSplat-Git/bugsplat-ng`
2. `cd bugsplat-ng && npm i`
3. `npm start`

The `npm start` command will build the sample application and upload source maps to BugSplat so that the JavaScript call stack can be mapped back to TypeScript. Once the build has completed the source maps will be uploaded and http-server will serve the app.

Navigate to the url displayed in the console by http-server (usually [localhost:8080](http://127.0.0.1:8080)). Click any of the button in the sample app to generate an error report. A link to the error report should display in the app shortly after clicking a button. Click the link to the error report and when prompted to log into BugSplat.

If everything worked correctly you should see information about your error as well as a TypeScript stack trace.

## Integration

To collect errors and crashes in your Angular application, run the following command in terminal or cmd at the root of your project to install bugsplat-ng:

```shell
npm i bugsplat-ng --save
```

Add a `database` property to your `package.json` file with the value of your BugSplat database and subsitute `{{database}}` with the value of your BugSplat database.

```json
{
  "database": "{{database}}"
}
```

Add values for your BugSplat database, application and version to your application's environment files:

[environment.prod.ts](https://github.com/BugSplat-Git/bugsplat-ng/blob/8c12d9b3544f2b618491467e6c40d84b6139eb2a/src/environments/environment.prod.ts#L1)

```typescript
const packageJson = require('../../package.json');
export const environment = {
  production: true,
  bugsplat: {
    database: packageJson.database,
    application: packageJson.name,
    version: packageJson.version
  }
};
```

Add an import for BugSplatModule to your AppModule:

[app.module.ts](hhttps://github.com/BugSplat-Git/bugsplat-ng/blob/8c12d9b3544f2b618491467e6c40d84b6139eb2a/src/app/app.module.ts#L4)

```typescript
import { BugSplatModule } from 'bugsplat-ng';
```

Add a call `BugSplatModule.initializeApp` in your AppModule's imports array passing it your database, application and version:

[app.module.ts](https://github.com/BugSplat-Git/bugsplat-ng/blob/8c12d9b3544f2b618491467e6c40d84b6139eb2a/src/app/app.module.ts#L31)

```typescript
...
@NgModule({
  imports: [
    BugSplatModule.initializeApp(environment.bugsplat)
  ],
  ...
})
```

Throw a new error in your application to test the bugsplat-ng integration:

[app.component.ts](https://github.com/BugSplat-Git/bugsplat-ng/blob/8c12d9b3544f2b618491467e6c40d84b6139eb2a/src/app/app.component.ts#L37)

```typescript
throw new Error("foobar!");
```

Navigate to the [Crashes](https://app.bugsplat.com/v2/crashes) page in BugSplat and you should see a new crash report for the application you just configured. Click the link in the ID column to see details about your crash on the Crash page:

<figure><img src="../../../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption><p>Crash Page</p></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (28).png" alt=""><figcaption><p>Crashes Page</p></figcaption></figure>

## Extended Integration

You can post additional information by creating a service that implements ErrorHandler. In the handlerError method make a call to BugSplat.post passing it the error and an optional options object:

[my-angular-error-handler.ts](https://github.com/BugSplat-Git/bugsplat-ng/blob/master/src/app/my-angular-error-handler.ts)

```typescript
import { ErrorHandler, Injectable } from '@angular/core';
import { BugSplat } from 'bugsplat-ng';

@Injectable()
export class MyAngularErrorHandler implements ErrorHandler {

    constructor(public bugsplat: BugSplat) { }
    
    async handleError(error: Error): Promise<void> {
        return this.bugsplat.post(error, {
            description: 'New description from MyAngularErrorHandler.ts'
        });
    }
}
```

BugSplat provides the following properties and methods that allow you to customize its functionality:

[bugsplat.ts](https://github.com/BugSplat-Git/bugsplat-ng/blob/master/projects/bugsplat-ng/src/lib/bugsplat.ts)

```typescript
BugSplat.description: string; // Additional info about your crash that gets reset after every post
BugSplat.email: string; // The email of your user 
BugSplat.key: string; // A unique identifier for your application
BugSplat.user: string; // The name or id of your user
BugSplat.files: Array<file>; // A list of files to be uploaded at post time
BugSplat.getObservable(): Observable<BugSplatPostEvent>; // Observable that emits results of BugSplat crash post events in your components.
async BugSplat.post(error): Promise<void>; // Post an Error object to BugSplat manually from within a try/catch
```

In your AppModule's NgModule definition, add a provider for your new ErrorHandler:

[app.module.ts](https://github.com/BugSplat-Git/bugsplat-ng/blob/master/src/app/app.module.ts)

```typescript
import { ErrorHandler, NgModule } from '@angular/core';

@NgModule({
  providers: [
    {
      provide: ErrorHandler,
      useClass: MyAngularErrorHandler
    }
  ]
  ...
})
```

You can also configure BugSplat's logging preferences and provide your own logging implementation. Create a provider for BugSplatLogger with useValue set to a new instance of BugSplatLogger. Pass one of the BugSplatLogLevel options as the first parameter to BugSplatLogger. You can provide an instance of your own custom logger as the second parameter granted it has error, warn, info and log methods. If no custom logger is provided console will be used:

[app.module.ts](https://github.com/BugSplat-Git/bugsplat-ng/blob/master/src/app/app.module.ts)

```typescript
import { ErrorHandler, NgModule } from '@angular/core';
import { BugSplatLogger, BugSplatLogLevel, BugSplatModule } from 'bugsplat-ng';

@NgModule({
  providers: [
    {
      provide: ErrorHandler,
      useClass: BugSplatErrorHandler
    },
    {
      provide: BugSplatLogger,
      useValue: new BugSplatLogger(BugSplatLogLevel.Log)
    }
  ],
  ...
})
```

## Source Maps

BugSplat supports unwinding uglified and minified JavaScript stack traces via source maps. To upload source maps to BugSplat during your build, install [@bugsplat/symbol-upload](https://www.npmjs.com/package/@bugsplat/symbol-upload).

```bash
npm i -D @bugsplat/symbol-upload
```

Configure your `angular.json` file to output source maps. We suggest enabling source maps for both your application code and any vendor chunks that are generated by Angular.

```js
{
  "projects": {
    "main": {
      "architect": {
        "build": {
          "options": {
            "sourceMap": {
              "scripts": true,
              "styles": true,
              "vendor": true
            },
          },
        }
      }
    }
  }
}
```

Add `SYMBOL_UPLOAD_CLIENT_ID` and `SYMBOL_UPLOAD_CLIENT_SECRET` environment variables for the BugSplat user that you will use to upload symbols. You can create these values as system environment variables or use [dotenv](https://www.npmjs.com/package/dotenv).

```bash
SYMBOL_UPLOAD_CLIENT_ID={{clientId}}
SYMBOL_UPLOAD_PASSWORD={{clientSecret}}
```

Create a script for uploading source maps.

```ts
const { OAuthClientCredentialsClient, SymbolsApiClient } = require('@bugsplat/symbol-upload');
const fs = require('fs');
const path = require('path');
const packageJson = require('../package.json');
require('dotenv').config();

(async () => {
    const clientId = process.env.SYMBOL_UPLOAD_CLIENT_ID;
    if (!clientId) {
        throw new Error('Please set SYMBOL_UPLOAD_CLIENT_ID in .env file');
    }

    const clientSecret = process.env.SYMBOL_UPLOAD_CLIENT_SECRET;
    if (!clientSecret) {
        throw new Error('Please set SYMBOL_UPLOAD_CLIENT_SECRET in .env file');
    }

    const database = packageJson.database;
    const application = packageJson.name;
    const version = packageJson.version;

    const buildDirectory = `./dist/${application}`;
    const files = fs.readdirSync(buildDirectory)
        .filter(file => file.endsWith('.js.map'))
        .map(file => {
            const filePath = `${buildDirectory}/${file}`;
            const stat = fs.statSync(filePath);
            const name = path.basename(filePath);
            const size = stat.size;
            return {
                name,
                size,
                file: fs.createReadStream(filePath)
            };
        });

    const bugsplat = await OAuthClientCredentialsClient.createAuthenticatedClient(clientId, clientSecret);
    const symbolsApiClient = new SymbolsApiClient(bugsplat);
    await symbolsApiClient.deleteSymbols(
        database,
        application,
        version
    );
    await symbolsApiClient.postSymbols(
        database,
        application,
        version,
        files
    );
    console.log(`Source maps uploaded to BugSplat ${database}-${application}-${version} successfully!`);
})().catch(error => console.error(error));
```

Add `postbuild` and `symbols` scripts to your `package.json`.

```json
{
  "scripts": {
    "postbuild": "npm run symbols",
    "symbols": "ts-node ./scripts/symbols.js"
  }
}
```

For best results, please upload source maps for every released version of your application.

## Contributing

BugSplat loves open source software! If you have suggestions on how we can improve this integration, please reach out to support@bugsplat.com, create an [issue](https://github.com/BugSplat-Git/bugsplat-ng/issues) in our [GitHub repo](https://github.com/BugSplat-Git/bugsplat-ng) or send us a [pull request](https://github.com/BugSplat-Git/bugsplat-ng/pulls).

With ❤️,

The BugSplat Team
