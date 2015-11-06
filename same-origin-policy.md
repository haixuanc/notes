Same origin policy:

```
protocol://host:port
```

Exempt from SOP:

- `<script>`

## 1. JSONP

*JSON is not valid JavaScript.* This is because JavaScript interpreters parse the opening curly brace as the beginning of a block statement, and expect it to be followed by one or more valid JavaScript statements.

### Solution 1. Global variables

Assign the return JSON data to a global variable. We listen to the `load` event of the `<script>` tag to know when the JSON data is received.

### Solution 2. Global callback functions

Pass thee returned JSON data to a global callback function. The advantage is that the global callback will be evaluated automatically when the `<script>` tag is loaded. No need to listen to the `load` event.

### Solution 3. Dynamic callback functions

Speficy the callback name in the requested URL: `http://path?callback=someGlobalFunction`. The callback function name is the **padding** part of JSONP.

In reality, generate a unique callback function name for each JSONP request to avoid poluting the global namespace.

An example:

```js
window.jsonpCallback = function(json) {
	console.log(json.title);
};

var script = document.createElement('script');
script.async = true; // set to false if you want to load the script synchronously
script.src = 'http://thirdparty.com/info.js?callback=jsonCallback';

document.body.appendChild(script);
```

### Limitations and security concerns

- `<script>` only supports `GET` requests. You won't be able to use other HTTP verbs like `POST`, `DELETE` to create a standard REST APIs.
- JSONP will always return **200** status code when the `<script>` is loaded. If the injected script has errors, nothing happens.
- `<script>` permits any malicious script content to be loaded and executed on the requesting page. It opens a door to *cross-site request forgery (CSRF)* attacks.

## 2. Subdomain proxies

## 3. CORS
