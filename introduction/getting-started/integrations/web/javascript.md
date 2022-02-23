# JavaScript

## Overview

BugSplat supports JavaScript and TypeScript error reporting in a variety of environments. Each environment that BugSplat supports requires slightly different configurations. Follow the links below for instructions on how to configure BugSplat in your application:

{% content-ref url="angular.md" %}
[angular.md](angular.md)
{% endcontent-ref %}

{% content-ref url="react.md" %}
[react.md](react.md)
{% endcontent-ref %}

{% content-ref url="vue.md" %}
[vue.md](vue.md)
{% endcontent-ref %}

{% content-ref url="../cross-platform/electron.md" %}
[electron.md](../cross-platform/electron.md)
{% endcontent-ref %}

{% content-ref url="../cross-platform/node.js.md" %}
[node.js.md](../cross-platform/node.js.md)
{% endcontent-ref %}

If a platform or framework your team is leveraging is not listed above, never fear! You can leverage our bugsplat [npm package](https://www.npmjs.com/package/bugsplat) to add error reporting to any browser-based app or [bugsplat-node](https://www.npmjs.com/package/bugsplat-node) to add error reporting to any Node.js based app.

{% hint style="info" %}
BugSplat supports unwinding uglified and minified JavaScript stack traces via source maps. More information about configuring your application to upload source maps to BugSplat is available [here](../../../development/working-with-symbol-files/source-maps.md).
{% endhint %}
