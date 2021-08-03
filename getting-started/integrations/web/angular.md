# Angular

## Introduction

#### Introduction <a id="intro"></a>

Before integrating a new BugSplat SDK with your application, make sure to review the [Getting Started](https://www.bugsplat.com/resources/bugsplat-101/) resources and complete the simple startup tasks listed below.

* [Sign up](https://app.bugsplat.com/v2/sign-up) for a BugSplat account
* [Log in](https://app.bugsplat.com/auth0/login) using your email address
* Create a new [database](https://app.bugsplat.com/v2/company) for your application

{% hint style="info" %}
 Need any further help? Check out the full BugSplat documentation [here](https://www.bugsplat.com/docs), or email the team at [support@bugsplat.com](mailto:support@bugsplat.com).
{% endhint %}

## Overview

Adding BugSplat to your Angular application requires only a few steps. Before getting any further please check out the [live demo](https://www.bugsplat.com/docs/sdk/angular/my-angular-crasher/) of BugSplat’s Angular error reporting tool.

## Simple Configuration

To collect errors and crashes in your Angular application, run the following command in terminal or cmd at the root of your project to install bugsplat-ng:

```
npm i bugsplat-ng --save
```

Import BugSplatErrorHandler and BugSplatConfiguration into your app module from bugsplat-ng:

[app.module.ts](https://github.com/BugSplat-Git/bugsplat-ng/blob/master/src/app/app.module.ts)

```bash
import { BugSplatErrorHandler, BugSplatConfig } from 'bugsplat-ng';
```

Add a provider for ErrorHandler with the useClass property set to BugSplatErrorHandler:

[app.module.ts](https://github.com/BugSplat-Git/bugsplat-ng/blob/master/src/app/app.module.ts)

```bash
import { ErrorHandler, NgModule } from '@angular/core';
import { BugSplatErrorHandler, BugSplatConfiguration } from 'bugsplat-ng';
...
@NgModule({
  providers: [
    { provide: ErrorHandler, useClass: BugSplatErrorHandler },
  ],
  ...
})
```

In your app module, add a provider for BugSplatConfiguration with the useValue property set to an instance of your BugSplat configuration:

[app.module.ts](https://github.com/BugSplat-Git/bugsplat-ng/blob/master/src/app/app.module.ts)

```bash
...
  const appName = 'my-angular-crasher';
  const appVersion = '1.0.0.0';
  const database = 'fred';
  
  @NgModule({
    providers: [
      { provide: ErrorHandler, useClass: BugSplatErrorHandler },
      { provide: BugSplatConfiguration, useValue: new BugSplatConfiguration(appName, appVersion, database) },
    ],
    ...
  })
```

Import the HttpClient module from @angular/common/http and add it to your module’s imports:

[app.module.ts](https://github.com/BugSplat-Git/bugsplat-ng/blob/master/src/app/app.module.ts)

```bash
import { HttpClientModule } from '@angular/common/http';
...
@NgModule({
  imports: [
    ...
    HttpClientModule
  ],
  providers: [
    { provide: ErrorHandler, useClass: BugSplatErrorHandler },
    { provide: BugSplatConfiguration, useValue: new BugSplatConfiguration(appName, appVersion, database) },
  ],
  ...
})
```



Throw a new error in your application to test the bugsplat-ng integration:

[app.component.ts](https://github.com/BugSplat-Git/bugsplat-ng/blob/master/src/app/app.component.ts)

```bash
throw new Error("BugSplat!");
```

Navigate to the [Crashes](http://app.bugsplat.com/v2/crashes/) page in BugSplat and you should see a new crash report for the application you just configured. Click the link in the Id column to see details about your crash on the Individual Crash page:

![](https://www.bugsplat.com/assets/img/docs/angular-crash-search-bugsplat.png)

![](https://www.bugsplat.com/assets/img/docs/angular-crash-example-bugsplat.png)

#### Extended Configuration <a id="extendedconfiguration"></a>

You can post additional information to BugSplat by creating a wrapper around the BugSplat object. To do so, create a new class that implements Angular’s ErrorHandler interface. In the handlerError method make a call to BugSplat.post passing it the error object:

[my-angular-error-handler.ts](https://github.com/BugSplat-Git/bugsplat-ng/blob/master/src/app/my-angular-error-handler.ts)

```bash
import { ErrorHandler, Injectable, Inject, Optional } from '@angular/core';
import { HttpClient } from "@angular/common/http";
import { BugSplatLogger, BugSplat, BugSplatConfiguration } from 'bugsplat-ng';
  
@Injectable()
export class MyAngularErrorHandler implements ErrorHandler {
  public bugsplat: BugSplat;

  constructor(
    @Inject(BugSplatConfiguration) public config: BugSplatConfiguration,
    @Inject(HttpClient) private httpClient: HttpClient,
    @Optional() private logger: BugSplatLogger
  ) {
      this.bugsplat = new BugSplat(this.config, this.httpClient);
  }
  
  handleError(error) {
    // Add additional functionality here
    this.bugsplat.post(error);
  }
}
```

BugSplat provides the following properties and methods that allow you to customize its functionality:

[bugsplat-error-handler.ts](https://github.com/BugSplat-Git/bugsplat-ng/blob/master/projects/bugsplat-ng/src/lib/bugsplat-error-handler.ts)

```bash
BugSplat.appkey: string; // A unique identifier for your application
BugSplat.user: string; // The name or id of your user
BugSplat.email: string; // The email of your user 
BugSplat.description: string; // Additional info about your crash that gets reset after every post
BugSplat.addAdditionalFile(file); // Add a file to a list of files to be uploaded at post time (total upload limit 2MB)
BugSplat.getObservable(); // Returns an Observable<BugSplatPostEvent>. Subscribing to this method will allow you to hook into the results of BugSplatPost events in your components. Make sure to unsubscribe in ngOnDestroy.
BugSplat.post(error); // Post an arbitrary Error object to BugSplat
view raw
```

In your app module, update the useClass property in your ErrorHandler provider to the name of the class you just created:

[app.module.ts](https://github.com/BugSplat-Git/bugsplat-ng/blob/master/src/app/app.module.ts)

```bash
...
  @NgModule({
    ...
    providers: [
      { provide: ErrorHandler, useClass: MyBugSplatErrorHandler },
      { provide: BugSplatConfiguration, useValue: new BugSplatConfiguration(appName, appVersion, database) },
    ],
    ...
  })
```

### Contributing

BugSplat loves open-source software! If you have suggestions on how we can improve this integration, please reach out to [support@bugsplat.com](mailto:support@bugsplat.com), create an [issue](https://github.com/BugSplat-Git/bugsplat-ng/issues) in our [GitHub repo](https://github.com/BugSplat-Git/bugsplat-ng) or send us a [pull request](https://github.com/BugSplat-Git/bugsplat-ng/pulls).

