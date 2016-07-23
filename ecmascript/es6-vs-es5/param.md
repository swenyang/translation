# 扩展参数处理 Extended Parameter Handling

## 默认参数值

函数参数可以直接使用简单、直观的默认值。

- ES6

	```js
	function f (x, y = 7, z = 42) {
	    return x + y + z
	}
	f(1) === 50
	```


- ES5

	```js
	function f (x, y, z) {
	    if (y === undefined)
	        y = 7;
	    if (z === undefined)
	        z = 42;
	    return x + y + z;
	};
	f(1) === 50;
	```

## 剩余参数

可以将可变参数函数的剩余参数整合到一个参数上。

- ES6

	```js
	function f (x, y, ...a) {
	    return (x + y) * a.length
	}
	f(1, 2, "hello", true, 7) === 9
	```


- ES5

	```js
	function f (x, y) {
	    var a = Array.prototype.slice.call(arguments, 2);
	    return (x + y) * a.length;
	};
	f(1, 2, "hello", true, 7) === 9;
	```

## 展开操作符

将可迭代集合（比如数组乃至字符串）的元素展开到字面量元素和单个函数参数中。

- ES6

	```js
	var params = [ "hello", true, 7 ]
	var other = [ 1, 2, ...params ] // [ 1, 2, "hello", true, 7 ]
	f(1, 2, ...params) === 9

	var str = "foo"
	var chars = [ ...str ] // [ "f", "o", "o" ]
	```


- ES5

	```js
	var params = [ "hello", true, 7 ];
	var other = [ 1, 2 ].concat(params); // [ 1, 2, "hello", true, 7 ]
	f.apply(undefined, [ 1, 2 ].concat(params)) === 9;

	var str = "foo";
	var chars = str.split(""); // [ "f", "o", "o" ]
	```
