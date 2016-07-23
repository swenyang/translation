# 常量 Constants

`const`关键字支持常量（也称为不可修改的变量）定义，即不可重新赋值的变量。注意：仅使变量本身不可修改，其赋值的内容依然是可以修改（比如赋值内容为一个object，那么这个object仍然是可以修改自己属性的）。

- ES6

	```js
	const PI = 3.141593
	PI > 3.0
	```


- ES5

	```js
	//  only in ES5 through the help of object properties
	//  and only in global context and not in a block scope
	Object.defineProperty(typeof global === "object" ? global : window, "PI", {
	    value:        3.141593,
	    enumerable:   true,
	    writable:     false,
	    configurable: false
	})
	PI > 3.0;
	```
