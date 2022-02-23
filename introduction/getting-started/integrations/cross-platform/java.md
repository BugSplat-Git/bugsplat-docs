# Java

## Overview

Integrate BugSplat crash reporting with your Java Applications:

Before doing anything make sure to [download](https://app.bugsplat.com/browse/download\_item.php/?item=java) the BugSplat software development kit for Java Applications.

## Add BugSplat to your application

Add the BugSplat pbrary to your CLASSPATH.

Import the BugSplat exception handler class `com.bugsplat.cpent.BugSplat`.

Add a call to BugSplat.Init as shown in the MyJavaCrasher sample code.

The initialization call requires three parameters: BugSplat database, application name and version. You supply the application name and version. The BugSplat database is created and selected on the [Versions](https://app.bugsplat.com/v2/versions) page.

Typically, you will create a new database for each major release of your product.

Add a try-catch block in your application entry point (for example, in main).

To handle both runtime Exceptions and Errors, catch Throwable, construct an Exception object, and pass it to BugSplat.HandleException.

If your application creates threads, you will want an exception report to be generated before the thread is terminated:

```
class MyThreadGroup extends ThreadGroup {
      public MyThreadGroup (String s) {
          super(s);
      }
              
      public void uncaughtException(Thread thread, Throwable throwable) {
          BugSplat.HandleException(new Exception(throwable));                     
      }
  }
```

Construct an instance of your `ThreadGroup` class.

Construct an instance of your Thread, providing your `ThreadGroup` instance in the constructor.

Once that is complete start the thread.

#### Test

Remember to test your with our the [MyJavaCrasher](../../posting-a-test-crash/myjavacrasher.md) sample application. This will test that crashes are posted and a good call stack is being created.
