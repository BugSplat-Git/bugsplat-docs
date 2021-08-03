# How to configure proxy settings in BugSplat

BugSplat can be configured to send crash reports through a proxy server using the following steps:

* Configure your proxy connection in Internet Explorer / Internet Options
* Select the Connections Tab
* Select the LAN settings button
* Enter the IP address and listening port of your proxy server
* The settings below are for a Squid proxy server, it listens to port 3128 by default. The correct values for your ip address and port are likely to be different.
* When this is setup correctly, Internet Explorer will be able to use the proxy server to show web pages

BugSplat will use these same settings to relay through the proxy server too.![ie proxy settings](https://www.bugsplat.com/assets/img/docs/ie-proxy.png)

