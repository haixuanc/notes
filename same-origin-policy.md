Same origin policy:

```
protocol://host:port
```

Exempt from SOP:

- JavaScript files: e.g. `<script>`

## 1. JSONP

- *JSON is not valid JavaScript.* This is because JavaScript interpreters parse the opening curly brace as the beginning of a block statement, and expect it to be followed by one or more valid JavaScript statements.

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


## 2. Subdomain proxies

## 3. CORS
