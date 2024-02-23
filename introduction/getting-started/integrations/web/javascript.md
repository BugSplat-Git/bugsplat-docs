# JavaScript

## 🧩 SPA Integrations

{% hint style="info" %}
BugSplat supports unwinding uglified and minified JavaScript stack traces via source maps. More information about configuring your application to upload source maps to BugSplat is available [here](../../../development/working-with-symbol-files/source-maps.md).
{% endhint %}

BugSplat supports JavaScript and TypeScript error reporting in a variety of environments. We offer platform-specific integrations for [Angular](https://angular.dev/), [React](https://react.dev/), and [Node.js](https://nodejs.org/en) for your convenience.&#x20;

Follow the links below for instructions on how to configure BugSplat in your application:

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

## 🌐 Browsers

Using the browser directly is awesome! BugSplat fully supports applications that don't use SPA frameworks. Follow the directions below to integrate with bugsplat in a browser-based JavaScript application.

### ⚙️ Integrating

Import [bugsplat](https://github.com/BugSplat-Git/bugsplat-js) from [esm.sh](https://esm.sh/)

```html
<script type="module">
    import { BugSplat } from 'https://esm.sh/bugsplat@8.0.1';
</script>
```

Create a new instance of BugSplat with your database, application, and version

```javascript
const bugsplat = new BugSplat('fred', 'my-javascript-crasher', '1.0.0');
```

Configure bugsplat to listen to `window.onerror` and `window.onunhandledrejection` events

```javascript
window.onerror = function (message, source, lineno, colno, error) {
    bugsplat.post(error);
};

window.addEventListener('unhandledrejection', function (event) {
    bugsplat.post(event.reason);
});
```

Trigger an error to see it reported in BugSplat

```javascript
throw new Error('todo bg');
```

### 🧪 Sample

Clone the [my-javascript-crasher](https://github.com/BugSplat-Git/my-javascript-crasher) repository

```sh
git clone https://github.com/BugSplat-Git/my-javascript-crasher
```

Install the dependencies

```sh
cd my-javascript-crasher
npm i
```

Start the server

```shell
npm start
```

Open your browser and navigate to [http://localhost:8080](http://localhost:8080/), then click the button to trigger an error.

### 📈 Viewing Reports

Navigate to the BugSplat [Crashes](https://app.bugsplat.com/v2/crashes?database=Fred\&c0=appName\&f0=CONTAINS\&v0=my-javascript-crasher) page to view your report

<figure><img src="../../../../.gitbook/assets/image (30).png" alt=""><figcaption><p>BugSplat Crashes Page</p></figcaption></figure>

Click on the ID of the most recent error to view the details of your report

<figure><img src="../../../../.gitbook/assets/image (31).png" alt=""><figcaption><p>BugSplat Crash Page</p></figcaption></figure>
