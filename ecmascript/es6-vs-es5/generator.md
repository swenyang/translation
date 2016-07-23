# Generators

Generator函数是ES6提供的一种异步编程解决方案，语法行为与传统函数完全不同。Generator函数的调用方法与普通函数一样，也是在函数名后面加上一对圆括号。不同的是，调用Generator函数后，该函数并不执行，返回的也不是函数运行结果，而是一个指向内部状态的指针对象。接下来调用遍历器对象的next方法，才会使指针移向下一个状态。也就是说，每次调用next方法，内部指针就从函数头部或上一次停下来的地方开始执行，直到遇到下一个yield语句（或return语句）为止。这样可以产生一定的序列值。

## 使用迭代器协议的Generator函数

迭代器协议也支持Generator。

- ES6

	```js
	let fibonacci = {
	    *[Symbol.iterator]() {
	        let pre = 0, cur = 1
	        for (;;) {
	            [ pre, cur ] = [ cur, pre + cur ]
	            yield cur
	        }
	    }
	}

	for (let n of fibonacci) {
	    if (n > 1000)
	        break
	    console.log(n)
	}
	```


- ES5

	```js
	var fibonacci = {
	    next: (function () {
	        var pre = 0, cur = 1;
	        return function () {
	            tmp = pre;
	            pre = cur;
	            cur += tmp;
	            return cur;
	        };
	    })()
	};

	var n;
	for (;;) {
	    n = fibonacci.next();
	    if (n > 1000)
	        break;
	    console.log(n);
	}
	```

## 直接使用Generator函数

- ES6

	```js
	function* range (start, end, step) {
	    while (start < end) {
	        yield start
	        start += step
	    }
	}

	for (let i of range(0, 10, 2)) {
	    console.log(i) // 0, 2, 4, 6, 8
	}
	```


- ES5

	```js
	function range (start, end, step) {
	    var list = [];
	    while (start < end) {
	        list.push(start);
	        start += step;
	    }
	    return list;
	}

	var r = range(0, 10, 2);
	for (var i = 0; i < r.length; i++) {
	    console.log(r[i]); // 0, 2, 4, 6, 8
	}
	```

## Generator匹配

- ES6

	```js
	let fibonacci = function* (numbers) {
	    let pre = 0, cur = 1
	    while (numbers-- > 0) {
	        [ pre, cur ] = [ cur, pre + cur ]
	        yield cur
	    }
	}

	for (let n of fibonacci(1000))
	    console.log(n)

	let numbers = [ ...fibonacci(1000) ]

	let [ n1, n2, n3, ...others ] = fibonacci(1000)
	```


- ES5

	```js
	//  no equivalent in ES5
	```

## Generator控制流

可以利用Generator实现"co-routines"和Promise风格的异步编程。

- ES6

	```js
	//  generic asynchronous control-flow driver
	function async (proc, ...params) {
	    var iterator = proc(...params)
	    return new Promise((resolve, reject) => {
	        let loop = (value) => {
	            let result
	            try {
	                result = iterator.next(value)
	            }
	            catch (err) {
	                reject(err)
	            }
	            if (result.done)
	                resolve(result.value)
	            else if (   typeof result.value      === "object"
	                     && typeof result.value.then === "function")
	                result.value.then((value) => {
	                    loop(value)
	                }, (err) => {
	                    reject(err)
	                })
	            else
	                loop(result.value)
	        }
	        loop()
	    })
	}

	//  application-specific asynchronous builder
	function makeAsync (text, after) {
	    return new Promise((resolve, reject) => {
	        setTimeout(() => resolve(text), after)
	    })
	}

	//  application-specific asynchronous procedure
	async(function* (greeting) {
	    let foo = yield makeAsync("foo", 300)
	    let bar = yield makeAsync("bar", 200)
	    let baz = yield makeAsync("baz", 100)
	    return `${greeting} ${foo} ${bar} ${baz}`
	}, "Hello").then((msg) => {
	    console.log("RESULT:", msg) // "Hello foo bar baz"
	})
	```

- ES5

	```js
	//  no equivalent in ES5
	```

## Generator方法

Generator方法可以定义在对象和类中。

- ES6

	```js
	class Clz {
	    * bar () {
	        …
	    }
	}
	let Obj = {
	    * foo () {
	        …
	    }
	}
	```


- ES5

	```js
	//  no equivalent in ES5
	```
