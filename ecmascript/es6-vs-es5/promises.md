# 异步编程Promises

## Promise的用法

Promise是异步编程的一种方案，ES6提供了Promise类来方便的实现异步编程功能。

- ES6

	```js
	function msgAfterTimeout (msg, who, timeout) {
	    return new Promise((resolve, reject) => {
	        setTimeout(() => resolve(`${msg} Hello ${who}!`), timeout)
	    })
	}
	msgAfterTimeout("", "Foo", 100).then((msg) =>
	    msgAfterTimeout(msg, "Bar", 200)
	).then((msg) => {
	    console.log(`done after 300ms:${msg}`)
	})
	```


- ES5

	```js
	function msgAfterTimeout (msg, who, timeout, onDone) {
	    setTimeout(function () {
	        onDone(msg + " Hello " + who + "!");
	    }, timeout);
	}
	msgAfterTimeout("", "Foo", 100, function (msg) {
	    msgAfterTimeout(msg, "Bar", 200, function (msg) {
	        console.log("done after 300ms:" + msg);
	    });
	});
	```

## Promise的结合

提供一个接口来整合多个Promise的执行，使你不用去管理底层的异步操作顺序。

- ES6

	```js
	function fetchAsync (url, timeout, onData, onError) {
	    …
	}
	let fetchPromised = (url, timeout) => {
	    return new Promise((resolve, reject) => {
	        fetchAsync(url, timeout, resolve, reject)
	    })
	}
	Promise.all([
	    fetchPromised("http://backend/foo.txt", 500),
	    fetchPromised("http://backend/bar.txt", 500),
	    fetchPromised("http://backend/baz.txt", 500)
	]).then((data) => {
	    let [ foo, bar, baz ] = data
	    console.log(`success: foo=${foo} bar=${bar} baz=${baz}`)
	}, (err) => {
	    console.log(`error: ${err}`)
	})
	```



- ES5

	```js
	function fetchAsync (url, timeout, onData, onError) {
	    …
	}
	function fetchAll (request, onData, onError) {
	    var result = [], results = 0;
	    for (var i = 0; i < request.length; i++) {
	        result[i] = null;
	        (function (i) {
	            fetchAsync(request[i].url, request[i].timeout, function (data) {
	                result[i] = data;
	                if (++results === request.length)
	                    onData(result);
	            }, onError);
	        })(i);
	    }
	}
	fetchAll([
	    { url: "http://backend/foo.txt", timeout: 500 },
	    { url: "http://backend/bar.txt", timeout: 500 },
	    { url: "http://backend/baz.txt", timeout: 500 }
	], function (data) {
	    var foo = data[0], bar = data[1], baz = data[2];
	    console.log("success: foo=" + foo + " bar=" + bar + " baz=" + baz);
	}, function (err) {
	    console.log("error: " + err);
	});
	```
