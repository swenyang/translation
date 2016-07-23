# 作用域 Scoping

## 块级作用域变量

`let`关键字可直接声明块级作用域的变量，不用像ES5一样作提前声明。

- ES6

	```js
	for (let i = 0; i < a.length; i++) {
	    let x = a[i]
	    …
	}
	for (let i = 0; i < b.length; i++) {
	    let y = b[i]
	    …
	}

	let callbacks = []
	for (let i = 0; i <= 2; i++) {
	    callbacks[i] = function () { return i * 2 }
	}
	callbacks[0]() === 0
	callbacks[1]() === 2
	callbacks[2]() === 4
	```


- ES5

	```js
	var i, x, y;
	for (i = 0; i < a.length; i++) {
	    x = a[i];
	    …
	}
	for (i = 0; i < b.length; i++)
	    y = b[i];
	    …
	}

	var callbacks = [];
	for (var i = 0; i <= 2; i++) {
	    (function (i) {
	        callbacks[i] = function() { return i * 2; };
	    })(i);
	}
	callbacks[0]() === 0;
	callbacks[1]() === 2;
	callbacks[2]() === 4;
	```

## 块级作用域函数

更方便的定义块级作用域函数。

- ES6

	```js
	{
	    function foo () { return 1 }
	    foo() === 1
	    {
	        function foo () { return 2 }
	        foo() === 2
	    }
	    foo() === 1
	}
	```


- ES5

	```js
	//  only in ES5 with the help of block-scope emulating
	//  function scopes and function expressions
	(function () {
	    var foo = function () { return 1; }
	    foo() === 1;
	    (function () {
	        var foo = function () { return 2; }
	        foo() === 2;
	    })();
	    foo() === 1;
	})();
	```
