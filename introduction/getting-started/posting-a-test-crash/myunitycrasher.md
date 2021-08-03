---
description: Testing Unity crashes with the sample application ‘myUnityCrasher’
---

# myUnityCrasher

## 

#### Overview

Before you enable your Unity project with BugSplat, you may want to take a moment to experiment with our `MyUnityCrasher` sample application.

First, [login](https://app.bugsplat.com/auth0/login) to BugSplat with the user name `fred@bugsplat.com` and password `Flintstone`.

All crash reports in the `fred@bugsplat.com` account are actual crashes created with our sample applications. You can see some sample `MyUnityCrasher` by navigating to the [Crashes](https://app.bugsplat.com/v2/crashes?database=Fred&c0=appName&f0=CONTAINS&v0=MyUnityCrasher) page.![bugsplat crashes page](https://www.bugsplat.com/assets/img/docs/myUnityCrasher-crashes.png)

### Windows Native C++

To begin using `MyUnityCrasher` [download](https://www.bugsplat.com/docs/sdk) the BugSplat Unity SDK. Once the SDK has been downloaded, right click the `BugSplatUnity.zip` file and choose `Extract All`.

Next, open Unity Hub and add the `MyUnityCrasher` project by clicking `Add` and selecting the folder `BugSplatUnity/BugSplat/samples/MyUnityCrasher`.

Once Unity has opened the `MyUnityCrasher` project, open the `Main` scene by double clicking it in the `Assets` window. Click `File > Build Settings` to open the build settings dialog. Ensure the `Copy PDB files` option is selected and click `Build and Run`. When prompted, select `BugSplatUnity/BugSplat/samples/MyUnityCrasher/bin` as the output location.

Once the window for the game appears, hold `Shift` and press the `m` key to trigger a program crash. The BugSplat dialog will be displayed and prompt you for input. Click the `Send Error Report` button at which point your internet browser will load a configurable support response.

![bugsplat sample crash unity](https://www.bugsplat.com/assets/img/docs/myUnityCrasher-sample.png)

Dismiss the support response and navigate to the [Crashes](https://app.bugsplat.com/v2/crashes?database=Fred&c0=appName&f0=CONTAINS&v0=MyUnityCrasher) page. On the `Crashes` page you should see a new `MyUnityCrasher` crash report. Click the link in the `ID` column to view details about the crash report.

![bugsplat crash page](https://www.bugsplat.com/assets/img/docs/myUnityCrasher-crash.png)

### Cross Platform .NET

Open Unity Hub and start a New Project. You can name this project whatever you would like. For this example, our project is named `MyUnityCrasher.`

Once in Unity, go to Asset and select `Custom Package` under `Import Package`. Once there open the BugSplatUnity.unitypackage file previously downloaded. This will place BugSplat into your Assets folder. Double-clicking the package will have the same effect.

Once it is in your asset folder, navigate to and select the folder `bugsplat > report > scene`. This will bring up the debug scene, which you will double-click on.  


![unitysamplecrasher-bugsplat-crashreport-2](https://www.bugsplat.com/assets/img/docs/unitysamplecrasher-bugsplat-crashreport-2-1.png)

Double-clicking will bring up the debug scene. Run this scene by hitting the run scene button \(play button\) at the top middle of the Unity window.

Once you do this, you will see in the Game Panel the scene UI showing three buttons. Any of these options will post a test crash, but for this example we will use the Prompted Exception button.

![myunitycrasher-bugsplat-crashreportingdemo](https://www.bugsplat.com/assets/img/docs/myunitycrasher-bugsplat-crashreportingdemo.gif)

By clicking that button, you will post the crash to the test database **fred@bugsplat.com**. To view it, navigate to the [Crashes](https://app.bugsplat.com/v2/allcrash) page.

To send a crash to a different database, set the database property on the Reporter object. Similarly, you can also set the app and version properties on the Reporter object to set the application name and version number respectively

